FROM jenkins/jenkins:lts-jdk17

USER root

WORKDIR /workspace

# Install prerequisites
RUN apt update && \
    apt install -y \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg \
    lsb-release \
    libpq-dev \
    openssl \
    nano

# Add Docker official GPG key
RUN install -m 0755 -d /etc/apt/keyrings && \
    curl -fsSL https://download.docker.com/linux/debian/gpg | gpg --dearmor -o /etc/apt/keyrings/docker.gpg && \
    chmod a+r /etc/apt/keyrings/docker.gpg

# Add Docker repository
RUN echo \
    "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/debian \
    "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
    tee /etc/apt/sources.list.d/docker.list > /dev/null

# Install Docker
RUN apt update && \
    apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# Create Docker group and add jenkins user to it
RUN groupadd -f docker && \
    usermod -aG docker jenkins && \
    mkdir -p /var/run

# Install System Packages needed for python projects
RUN apt install -y python3 python3-pip python3-venv python3-setuptools 

RUN rm -rf /var/lib/apt/lists/*

# Install trivy for security scanning
RUN curl -sfL https://raw.githubusercontent.com/aquasecurity/trivy/main/contrib/install.sh | sh -s -- -b /usr/local/bin

# 
# Creates a workspace directory in root, sets jenkins user and group as owner,
# and grants read & execute permissions to all users, write permission to owner.
# This ensures Jenkins has a dedicated workspace with proper access rights
RUN mkdir -p /workspace && \
    chown -R jenkins:jenkins /workspace && \
    chmod 755 /workspace

# Create a pytest.ini file in the workspace directory if it doesn't exist and set the testpaths
RUN echo '[pytest]\npython_files = tests.py test_*.py *_tests.py\ntestpaths = tests' > /workspace/pytest.ini && \
    chown jenkins:jenkins /workspace/pytest.ini && \
    chmod 644 /workspace/pytest.ini

# Create the docker group in container and add jenkins user to it
RUN groupadd -f -g 999 docker && \
    usermod -aG docker jenkins

# Switch back to jenkins user
USER jenkins