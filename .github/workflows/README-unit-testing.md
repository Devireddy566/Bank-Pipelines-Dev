# Unit Testing Pipeline

This pipeline (`unit-testing.yml`) is designed for developers to test their feature branches during development.

## Purpose
- Provides quick feedback on code quality and test results
- Runs unit tests and optional code analysis
- Ensures code quality before merging to main branches

## Features
- Manual trigger with test type selection
- Unit test execution
- Code analysis with SonarQube (optional)
- Secret scanning with Gitleaks
- Comprehensive test reporting
- Maven build caching for faster runs

## Prerequisites
The following secrets must be configured in your GitHub repository:
- `SONAR_URL`: URL of your SonarQube instance
- `SONAR_TOKEN`: Authentication token for SonarQube

## Usage

1. Go to the "Actions" tab in your GitHub repository
2. Select "Unit Testing Pipeline"
3. Click "Run workflow"
4. Select your test type:
   - `Unit Tests`: Quick test run without analysis
   - `Unit Tests with Analysis`: Full test run with SonarQube analysis
5. Click "Run workflow" to start the pipeline

## Pipeline Steps
1. **Checkout**: Retrieves pipeline and application code
2. **Secret Scan**: Runs Gitleaks (only with full analysis)
3. **Test Execution**: Runs unit tests with Maven
4. **Code Analysis**: Performs SonarQube analysis (only with full analysis)
5. **Report Generation**: Creates and uploads test reports

## Artifacts
The pipeline generates the following artifacts:
- Test reports (JUnit format)
- Code coverage reports (JaCoCo)
- Gitleaks security scan report (if applicable)

## Runtime
- Typically takes 3-5 minutes for unit tests only
- 5-10 minutes for full analysis
