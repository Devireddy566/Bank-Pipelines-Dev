# Simplified DEV CI Pipeline

## Structure
```
Pk-Pipelines/
├── configs/
│   └── env/
│       └── dev.yml      # Environment configuration
├── DEV/
│   └── pipeline.yml     # Main pipeline
└── docs/
    └── pipeline-setup.md
```

## Quick Start
1. Configure environment in `configs/env/dev.yml`
2. Set up required secrets in GitHub
3. Run pipeline with app name and version

## Requirements
- Self-hosted runner with Docker
- GCP and SonarQube access
- GitHub repository permissions

## Maintenance

### Regular Tasks
1. Clean Docker system: `docker system prune -af`
2. Monitor disk space usage
3. Update runner dependencies
4. Rotate secrets and tokens

### Troubleshooting
1. Check Docker daemon status
2. Verify network connectivity
3. Ensure sufficient disk space
4. Review runner permissions

## Support
For issues contact:
- Pipeline issues: DevOps team
- Application issues: Development team
- Infrastructure issues: Platform team
## GitHub Repository Configuration

### Required Secrets
```yaml
# GCP Configuration
GCP_PROJECT_ID: "your-project-id"
GCP_SERVICE_ACCOUNT: "base64-encoded-json-key"
GCR_REGISTRY: "gcr.io"

# SonarQube Configuration
SONAR_URL: "https://your-sonarqube-url"
SONAR_TOKEN: "sonar-auth-token"

# Repository Access
APP_REPO_TOKEN: "github-pat-with-repo-access"
```

### Repository Permissions
- Actions: Read/Write
- Packages: Read/Write
- Contents: Read
- Pull Requests: Read/Write

### Environment Setup
1. Create 'dev' environment in repository settings
2. Configure environment protection rules if needed
3. Add environment-specific secrets if required

## Container Requirements

### Containers Used
1. **Gitleaks**: Secret scanning
   - Image: `zricethezav/gitleaks`
   - Privileges: root, docker socket access

2. **Maven**: Build and Test
   - Image: `maven:3.8-openjdk-17`
   - Volume mounts: Maven cache, workspace

3. **Trivy**: Security scanning
   - Image: `aquasec/trivy:latest`
   - Privileges: root, docker socket access

## Maintenance

### Regular Tasks
1. Clean Docker system: `docker system prune -af`
2. Monitor disk space usage
3. Update runner dependencies
4. Rotate secrets and tokens

### Troubleshooting
1. Check Docker daemon status
2. Verify network connectivity
3. Ensure sufficient disk space
4. Review runner permissions

## Pipeline Triggers
- Manual trigger (workflow_dispatch)
- Required inputs:
  - Application name (from predefined list)
  - Tag version

## Support
For issues contact:
- Pipeline issues: DevOps team
- Application issues: Development team
- Infrastructure issues: Platform team
