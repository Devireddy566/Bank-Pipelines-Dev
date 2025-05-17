# Self-Hosted Runner Setup Guide

## System Requirements
- OS: Ubuntu 20.04 LTS (recommended)
- CPU: 2 cores minimum
- RAM: 4GB minimum
- Storage: 50GB minimum
- Network: Stable internet connection

## Basic Setup
```bash
# Update system
sudo apt-get update
sudo apt-get upgrade -y

# Install required packages
sudo apt-get install -y \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg \
    lsb-release
```

## Docker Installation
```bash
# Add Docker's official GPG key
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

# Add Docker repository
echo \
  "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Install Docker
sudo apt-get update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io

# Start Docker service
sudo systemctl start docker
sudo systemctl enable docker

# Add runner user to docker group
sudo usermod -aG docker $USER
```

## GitHub Runner Setup
```bash
# Create directory for runner
mkdir actions-runner && cd actions-runner

# Download runner (replace {latest} with current version)
curl -o actions-runner-linux-x64-{latest}.tar.gz -L https://github.com/actions/runner/releases/download/v{latest}/actions-runner-linux-x64-{latest}.tar.gz

# Extract runner
tar xzf ./actions-runner-linux-x64-{latest}.tar.gz

# Configure runner
./config.sh --url https://github.com/[YOUR_ORG]/[YOUR_REPO] --token [YOUR_TOKEN] --labels Runner-1

# Install runner as service
sudo ./svc.sh install
sudo ./svc.sh start
```

## Required Directories
```bash
# Create Maven cache directory
mkdir -p ~/.m2

# Create Docker cache directory
mkdir -p ~/.docker-cache
```

## File Permissions
```bash
# Set proper permissions
sudo chown -R $USER:$USER ~/.m2
sudo chown -R $USER:$USER ~/.docker-cache
```

## Verify Installation
```bash
# Check Docker
docker --version
docker ps

# Check runner service
sudo systemctl status actions.runner.*

# Check directories
ls -la ~/.m2
ls -la ~/.docker-cache
```
