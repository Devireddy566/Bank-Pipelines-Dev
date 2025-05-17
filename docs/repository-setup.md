# Repository Configuration Guide

## Required Secrets

### GCP Configuration
```yaml
GCP_PROJECT_ID: "your-gcp-project-id"
GCP_SERVICE_ACCOUNT: "base64-encoded-service-account-key"
GCR_REGISTRY: "gcr.io"
```

### SonarQube Configuration
```yaml
SONAR_URL: "https://your-sonarqube-url"
SONAR_TOKEN: "your-sonarqube-token"
```

## Environment Setup

1. Create Development Environment
   - Go to Settings > Environments
   - Click "New environment"
   - Name it "dev"
   - Configure protection rules if needed

2. Configure Branch Protection
   - Go to Settings > Branches
   - Add rule for `main` branch
   - Enable required status checks
   - Enable required reviews

## Repository Permissions

Required permissions for service accounts:
- Actions: Read/Write
- Packages: Read/Write
- Contents: Read
- Pull Requests: Read/Write

## Runner Labels
Configure runner with label:
- `self-hosted`
- `Runner-1`

## Directory Structure
```
Pk-Pipelines/
├── .github/
│   └── workflows/
│       └── dev-pipeline.yml
├── DEV/
│   └── pipeline.yml
├── configs/
│   └── env/
│       └── dev.yml
└── docs/
    ├── runner-setup.md
    └── repository-setup.md
```
