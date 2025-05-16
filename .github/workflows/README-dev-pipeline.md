# DEV CI Pipeline

This pipeline (`dev-pipeline.yml`) is responsible for building, testing, and deploying applications to the DEV environment.

## Purpose
- Builds and tests applications
- Performs security scans
- Creates and publishes Docker images
- Deploys to DEV environment

## Features
- Manual trigger with application selection
- Multi-stage pipeline with security checks
- Docker image building and scanning
- GCR (Google Container Registry) integration
- SonarQube code analysis
- Maven build with caching

## Prerequisites

### Required Secrets
The following secrets must be configured in your GitHub repository:
- `GCP_PROJECT_ID`: Your Google Cloud Project ID
- `GCP_SERVICE_ACCOUNT`: GCP service account credentials JSON
- `GCR_REGISTRY`: Google Container Registry URL
- `SONAR_URL`: SonarQube instance URL
- `SONAR_TOKEN`: SonarQube authentication token
- `APP_REPO_TOKEN`: GitHub token with repository access

## Usage

1. Go to the "Actions" tab in your GitHub repository
2. Select "DEV CI Pipeline"
3. Click "Run workflow"
4. Select the application to deploy from the dropdown
5. Click "Run workflow" to start the pipeline

## Pipeline Stages

### 1. Checkout (`checkout-app`)
- Checks out pipeline repository
- Checks out application repository
- Uploads source code as artifact

### 2. Security Check (`security-check`)
- Downloads source code
- Runs Gitleaks for secret detection
- Generates security report

### 3. Build (`build`)
- Uses Maven to build the application
- Caches Maven dependencies
- Creates JAR artifact

### 4. Test (`test`)
- Runs unit tests
- Performs SonarQube analysis
- Enforces quality gates

### 5. Scan and Push (`scan-and-push`)
- Builds Docker image
- Scans image with Trivy
- Authenticates with GCP
- Pushes image to GCR

## Artifacts
The pipeline generates the following artifacts:
- Application JAR file
- Gitleaks security report
- Trivy container scan report
- Docker image in GCR

## Runtime
- Typical full pipeline run: 15-20 minutes
- Varies based on application size and build complexity

## Docker Images
Images are tagged with:
- Version tag: `dev:{commit-sha}`
- Latest tag: `dev:latest`

## Notes
- The pipeline uses a self-hosted runner labeled 'Runner-1'
- All stages run in containers for consistency
- Failed security scans will block deployment
- SonarQube quality gates must pass for successful deployment
