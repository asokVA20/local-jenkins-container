pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'ghcr.io/USERNAME/django_app:latest'
        GITHUB_CREDENTIALS_ID = '111'
        DJANGO_SETTINGS_MODULE = 'django_app.settings'
        VENV_PATH = "/workspace/venv"
        PATH = "${VENV_PATH}/bin:${env.PATH}"
    }

    stages {
        stage('Setup') {
            steps {
                script {
                    sh '''#!/bin/bash
                        # Use the pre-created virtual environment
                        . ${VENV_PATH}/bin/activate
                        
                        # Install project dependencies
                        pip install -r requirements.txt
                    '''
                }
            }
        }

        stage('Security Scan') {
            steps {
                sh '''#!/bin/bash
                    source ${VENV_PATH}/bin/activate
                    
                    bandit -r . -f json -o bandit-results.json || true
                    safety check -r requirements.txt --json > safety-results.json || true
                '''
            }
        }

        stage('Test') {
            steps {
                sh '''#!/bin/bash
                    # Run tests with coverage
                    python -m pytest \
                        --cov=. \
                        --cov-report=xml \
                        --junitxml=test-results.xml \
                        -v || true  # Add -v for verbose output and || true to prevent pipeline failure
                '''
            }
        }

        stage('Code Quality') {
            steps {
                sh '''#!/bin/bash
                    source ${VENV_PATH}/bin/activate
        
                    flake8 . --output-file=flake8-results.txt || true
                    black --check . || true
                    isort --check-only . || true
                '''
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build(DOCKER_IMAGE, "-f .devcontainer/Dockerfile .")
                }
            }
        }

        stage('Docker Security Scan') {
            steps {
                sh '''
                    docker scan $DOCKER_IMAGE || true
                    trivy image ${DOCKER_IMAGE} || true
                '''
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: GITHUB_CREDENTIALS_ID, usernameVariable: 'USERNAME', passwordVariable: 'TOKEN')]) {
                    sh '''
                        echo $TOKEN | docker login ghcr.io -u $USERNAME --password-stdin
                        docker push $DOCKER_IMAGE
                    '''
                }
            }
        }
    }

    post {
        always {
            // Archive test and security scan results
            archiveArtifacts artifacts: '**/test-results.xml, **/bandit-results.json, **/safety-results.json, **/flake8-results.txt', allowEmptyArchive: true
            junit '**/test-results.xml'
            
            recordCoverage(
                tools: [[parser: 'COBERTURA', pattern: 'coverage.xml']],
                id: 'python-coverage',
                name: 'Python Coverage',
                sourceCodeRetention: 'EVERY_BUILD'
            )

            // Cleanup
            sh 'docker system prune -f'
            deleteDir()
        }
        
        failure {
            emailext (
                subject: "Pipeline Failed: ${currentBuild.fullDisplayName}",
                body: "Pipeline failed. Check console output at ${BUILD_URL}",
                recipientProviders: [[$class: 'DevelopersRecipientProvider']],
                to: 'email@domain.com'
            )
        }

        success {
            emailext (
                subject: "Pipeline Succeeded: ${currentBuild.fullDisplayName}",
                body: "Pipeline succeeded. Check console output at ${BUILD_URL}",
                recipientProviders: [[$class: 'DevelopersRecipientProvider']],
                to: 'email@domain.com'
            )
        }
    }
}
