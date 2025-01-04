# Local Jenkins Container running in a VS Code Dev Container

A professional CI/CD development environment with Jenkins configured for Django projects using VS Code Dev Containers. This setup provides a fully configured Jenkins environment with Docker support, Python tools, and security scanning capabilities.

## Features

- 🐳 Containerized Jenkins environment with Docker-in-Docker support
- 🐍 Complete Python/Django development tooling
- 🔒 Built-in security scanning (Trivy, Bandit)
- 🧪 Integrated testing suite (pytest, pytest-django, pytest-cov)
- 📊 Code quality tools (flake8, black, isort)
- 🔄 Automated CI/CD pipeline
- 🛠️ VS Code integration with essential extensions

## Prerequisites

- Docker Desktop / Docker installed
- Visual Studio Code
- VS Code Remote - Containers extension

## Quick Start

1. Clone this repository
2. Open in VS Code
3. When prompted, click "Reopen in Container" if not then run "Rebuild and Reopen in Container"
4. Access Jenkins at `http://localhost:8080`

## Environment Components

### Dev Container Setup
- Jenkins LTS with JDK 17
- Python development environment
- Docker-in-Docker capability
- Pre-configured VS Code extensions
- Exposed ports: 8080 (Jenkins UI), 50000 (Jenkins agents)

### Included Tools

#### Python Tools
- pytest with Django support
- Coverage reporting
- Flake8, Black, isort
- Bandit for security scanning
- Various linting and formatting tools

#### Security Tools
- Trivy for container scanning
- Docker security scanning
- Dependency scanning

## Jenkins Pipeline

The included Jenkinsfile provides a template for a comprehensive CI/CD pipeline that you can customize for your Django projects. This template demonstrates best practices for automated workflows including:

1. Environment Setup
   - Creates isolated Python virtual environment
   - Installs project dependencies

2. Testing & Quality
   - Runs pytest with coverage reporting
   - Performs code quality checks (flake8, black, isort)
   - Generates test reports and coverage metrics

3. Security Analysis
   - Scans Python dependencies for vulnerabilities
   - Performs static code analysis with Bandit
   - Checks Docker images with Trivy

4. Container Management
   - Builds Docker images from your project
   - Performs container security scanning
   - Publishes to container registry (configured for GitHub Packages)

5. Notifications
   - Sends email notifications on pipeline success/failure
   - Archives test results and security reports
   - Maintains build artifacts

To use this pipeline:
1. Update the environment variables in the Jenkinsfile:
   - `DOCKER_IMAGE`: Your container registry path
   - `GITHUB_CREDENTIALS_ID`: Your Jenkins credentials ID
   - `DJANGO_SETTINGS_MODULE`: Your Django settings path

2. Configure your container registry authentication (e.g. GitHub Packages, Docker Hub, GitLab Container Registry, etc.)
3. Adjust the notification settings to your email in Jenkins UI
4. Modify the stages according to your project needs

The pipeline is designed to be modular - you can easily add, remove, or modify stages to match your development workflow.

## VS Code Development Environment

The development container comes pre-configured with a comprehensive Python/Django development environment alongside Jenkins CI/CD capabilities. The following VS Code extensions are automatically installed to enhance your development experience:

### Python Development Extensions
- Python extension (IntelliSense, debugging, code navigation)
- Pylance (fast, feature-rich language support)
- Black formatter (PEP-compliant code formatting)
- Flake8 (code style enforcement)
- isort (import statement organization)
- Autopep8 (PEP 8 style guide automation)

### Development Features
- Full Django development support
- Integrated debugging configuration
- Auto-completion and IntelliSense
- Real-time linting and error detection
- Auto-formatting on save
- Import organization
- Virtual environment management

### Usage
1. Open your Django project in the container
2. The Python interpreter will automatically be configured
3. Format-on-save is enabled by default using Black
4. Linting runs automatically using Flake8
5. Imports are automatically sorted using isort

All extensions are configured to work with Django-specific syntax and patterns out of the box.

## Maintenance

Dependencies are automatically managed through GitHub Dependabot with weekly updates for dev containers.

## Security

The environment includes:
- Docker socket security configuration
- Container security scanning
- Dependency vulnerability checking
- Code security analysis

## Contributing (if you want to)

1. Fork the repository
2. Create your feature branch
3. Commit your changes
4. Push to the branch
5. Create a new Pull Request

