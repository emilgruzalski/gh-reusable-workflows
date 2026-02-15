# Security Scan Action

Scans source code and dependencies for security vulnerabilities using Trivy with GitHub Security integration.

## Features

- **Comprehensive Scanning**: Detects vulnerabilities in dependencies, OS packages, and configuration files
- **Multiple Severities**: Configurable severity levels (CRITICAL, HIGH, MEDIUM, LOW, UNKNOWN)
- **SARIF Upload**: Automatically uploads results to GitHub Security tab
- **Fail or Warn**: Choose whether to fail the workflow or just warn
- **Always Runs**: Can be configured to run even if previous steps fail
- **Multi-Language**: Supports Go, Node.js, Python, Java, Ruby, Rust, and more

## Usage

### Basic Usage (Fail on Critical/High)

```yaml
- name: Security scan
  uses: emilgruzalski/gh-reusable-workflows/.github/actions/scan-image@v1
```

### Scan with Custom Severity

```yaml
- name: Security scan
  uses: emilgruzalski/gh-reusable-workflows/.github/actions/scan-image@v1
  with:
    severity: 'CRITICAL,HIGH,MEDIUM'
```

### Warn Only (Don't Fail Build)

```yaml
- name: Security scan
  uses: emilgruzalski/gh-reusable-workflows/.github/actions/scan-image@v1
  with:
    exit-code: '0'  # Don't fail the workflow
```

## Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `working-directory` | Directory containing source code and dependencies to scan | No | `.` |
| `severity` | Comma-separated list of severity levels to report | No | `CRITICAL,HIGH` |
| `exit-code` | Exit code when vulnerabilities are found (1=fail, 0=warn) | No | `1` |
| `upload-sarif` | Whether to upload results to GitHub Security tab | No | `true` |

## Severity Levels

- `CRITICAL`: Critical vulnerabilities requiring immediate action
- `HIGH`: High-severity vulnerabilities that should be fixed soon
- `MEDIUM`: Medium-severity issues
- `LOW`: Low-severity issues
- `UNKNOWN`: Vulnerabilities with unknown severity

## Required Permissions

```yaml
permissions:
  security-events: write  # Required for uploading to Security tab
  contents: read          # Required for scanning repository
```

## Examples

### Scan Specific Directory

```yaml
- name: Scan backend
  uses: emilgruzalski/gh-reusable-workflows/.github/actions/scan-image@v1
  with:
    working-directory: './backend'
```

### Scan All Severities

```yaml
- name: Comprehensive scan
  uses: emilgruzalski/gh-reusable-workflows/.github/actions/scan-image@v1
  with:
    severity: 'UNKNOWN,LOW,MEDIUM,HIGH,CRITICAL'
```

### Continue on Vulnerabilities (CI/CD Insight)

```yaml
- name: Security scan (non-blocking)
  uses: emilgruzalski/gh-reusable-workflows/.github/actions/scan-image@v1
  with:
    exit-code: '0'
    upload-sarif: 'true'
```

### Scan Without GitHub Security Upload

```yaml
- name: Security scan
  uses: emilgruzalski/gh-reusable-workflows/.github/actions/scan-image@v1
  with:
    upload-sarif: 'false'
```

## What It Scans

### Dependency Files

Trivy scans common dependency manifest files:

- **Node.js**: `package-lock.json`, `yarn.lock`, `pnpm-lock.yaml`
- **Python**: `Pipfile.lock`, `poetry.lock`, `requirements.txt`
- **Go**: `go.mod`, `go.sum`
- **Java**: `pom.xml`, `build.gradle`, `build.gradle.kts`
- **Ruby**: `Gemfile.lock`
- **.NET**: `packages.lock.json`, `*.csproj`
- **PHP**: `composer.lock`
- **Rust**: `Cargo.lock`

### Configuration Files

- Terraform files (`.tf`)
- Kubernetes manifests (`.yaml`, `.yml`)
- Dockerfile
- Docker Compose files

### Security Issues Detected

- Known CVEs in dependencies
- Outdated packages with security patches
- Misconfigured infrastructure-as-code
- Exposed secrets (limited)
- License compliance issues

## Viewing Results

### In GitHub Security Tab

1. Go to repository → Security → Code scanning alerts
2. View all detected vulnerabilities
3. See detailed information, affected files, and remediation advice
4. Track remediation over time

### In Workflow Logs

Trivy outputs a summary in the workflow logs showing:
- Total vulnerabilities found
- Breakdown by severity
- Affected packages

## Complete Workflow Example

```yaml
name: Security Scan

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]
  schedule:
    - cron: '0 0 * * 0'  # Weekly scan

jobs:
  security:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      security-events: write

    steps:
      - uses: actions/checkout@v6

      - name: Security scan
        uses: emilgruzalski/gh-reusable-workflows/.github/actions/scan-image@v1
        with:
          severity: 'CRITICAL,HIGH'
          exit-code: '1'
```

## Trivy Database Updates

Trivy automatically downloads the latest vulnerability database before scanning, ensuring up-to-date vulnerability detection.

## Comparison with Other Scanners

| Feature | Trivy | Snyk | Dependabot |
|---------|-------|------|------------|
| Open Source | ✅ | ❌ | ❌ |
| Multi-language | ✅ | ✅ | ✅ |
| IaC Scanning | ✅ | ✅ | ❌ |
| Container Images | ✅ | ✅ | ❌ |
| License Detection | ✅ | ✅ | ❌ |
| Free for Private Repos | ✅ | Limited | ✅ |

## Troubleshooting

### "Permission denied" when uploading SARIF

**Solution**: Add `security-events: write` permission to your workflow.

### Scan takes too long

**Solutions**:
- Scan specific directories instead of entire repository
- Use `.trivyignore` to skip certain paths
- Run scans less frequently (e.g., on schedule instead of every push)

### Too many false positives

**Solutions**:
- Adjust severity levels to focus on CRITICAL and HIGH only
- Use `.trivyignore` to suppress specific vulnerabilities
- Update dependencies to latest versions

## Learn More

- [Trivy Documentation](https://aquasecurity.github.io/trivy/)
- [GitHub Code Scanning](https://docs.github.com/en/code-security/code-scanning)
- [SARIF Format](https://sarifweb.azurewebsites.net/)
