# GitHub Reusable Workflows

[![License: Apache 2.0](https://img.shields.io/badge/License-Apache_2.0-blue.svg)](LICENSE)

Production-ready, security-focused reusable GitHub Actions workflows and composite actions for Go, Node.js, and Python projects.

## Overview

This repository provides a collection of enterprise-grade, reusable GitHub Actions workflows and composite actions that implement modern DevOps best practices including:

- 🔒 **Security-First Design** - All actions pinned to commit SHAs, vulnerability scanning, secret detection, and container image signing
- 🚀 **Multi-Language Support** - Purpose-built workflows for Go, Node.js, and Python with language-specific optimizations
- 📦 **Complete CI/CD Pipeline** - Code quality checks, testing, container builds, SBOM generation, and deployment
- ⚡ **Performance Optimized** - Intelligent caching, parallel execution, and efficient resource utilization
- 📊 **Observability** - Rich job summaries, coverage reports, and security scan results

## Features

### 🔧 Composite Actions

| Action | Description | Languages |
|--------|-------------|-----------|
| [cq-go](/.github/actions/cq-go) | Go code quality: vet, lint (golangci-lint), test with coverage | Go |
| [cq-node](/.github/actions/cq-node) | Node.js code quality: lint, test, build using Bun | Node.js |
| [cq-py](/.github/actions/cq-py) | Python code quality: lint (Ruff), format check, test | Python |
| [build-image](/.github/actions/build-image) | Multi-arch Docker image builds with GHCR push | All |
| [scan-image](/.github/actions/scan-image) | Vulnerability scanning with Trivy and SARIF upload | All |
| [sign-image](/.github/actions/sign-image) | Keyless container signing with Sigstore/Cosign | All |
| [sbom](/.github/actions/sbom) | Generate CycloneDX SBOM with optional Dependency Track upload | All |

### 🔄 Reusable Workflows

| Workflow | Description | Use Case |
|----------|-------------|----------|
| [ci-go.yml](/.github/workflows/ci-go.yml) | Complete Go CI pipeline with quality checks, security scanning, image build & sign | Go services |
| [ci-node.yml](/.github/workflows/ci-node.yml) | Complete Node.js CI pipeline with Bun, security scanning, image build & sign | Node.js apps |
| [ci-py.yml](/.github/workflows/ci-py.yml) | Complete Python CI pipeline with uv, security scanning, image build & sign | Python apps |
| [dependency-review.yml](/.github/workflows/dependency-review.yml) | PR-based dependency vulnerability and license compliance checking | All projects |
| [secret-scan.yml](/.github/workflows/secret-scan.yml) | Secret detection in repository history using TruffleHog | All projects |
| [cd-az-stapp.yml](/.github/workflows/cd-az-stapp.yml) | Deploy to Azure Static Web Apps | Frontend apps |

## Getting Started

### Prerequisites

- GitHub repository with code in Go, Node.js, or Python
- GitHub Actions enabled
- (Optional) GitHub Container Registry enabled for Docker image publishing

### Quick Start

1. **Choose a workflow template** from [`.github/workflow-templates/`](/.github/workflow-templates/)

2. **Copy to your repository** at `.github/workflows/ci.yml`

3. **Customize the inputs**:

#### For Go Projects

```yaml
name: CI

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  ci:
    uses: emilgruzalski/gh-reusable-workflows/.github/workflows/ci-go.yml@v1
    with:
      image-name: 'my-go-service'
      go-version-file: 'go.mod'
      dockerfile: './Dockerfile'
      push: ${{ github.event_name == 'push' && github.ref == 'refs/heads/main' }}
    permissions:
      contents: write
      packages: write
      security-events: write
      id-token: write
```

#### For Node.js Projects

```yaml
name: CI

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  ci:
    uses: emilgruzalski/gh-reusable-workflows/.github/workflows/ci-node.yml@v1
    with:
      image-name: 'my-node-app'
      node-version-file: 'package.json'
      push: ${{ github.event_name == 'push' && github.ref == 'refs/heads/main' }}
    permissions:
      contents: write
      packages: write
      security-events: write
      id-token: write
```

#### For Python Projects

```yaml
name: CI

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  ci:
    uses: emilgruzalski/gh-reusable-workflows/.github/workflows/ci-py.yml@v1
    with:
      image-name: 'my-python-api'
      python-version-file: 'pyproject.toml'
      push: ${{ github.event_name == 'push' && github.ref == 'refs/heads/main' }}
    permissions:
      contents: write
      packages: write
      security-events: write
      id-token: write
```

4. **Commit and push** - The workflow will run automatically!

## Usage Examples

### Using Individual Composite Actions

You can also use individual composite actions in your own workflows:

```yaml
jobs:
  quality:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v6

      - name: Code Quality
        uses: emilgruzalski/gh-reusable-workflows/.github/actions/cq-go@v1
        with:
          working-directory: './backend'

      - name: Security Scan
        uses: emilgruzalski/gh-reusable-workflows/.github/actions/scan-image@v1
        with:
          severity: 'CRITICAL,HIGH'
```

### Dependency Review on PRs

```yaml
name: Dependency Review

on:
  pull_request:
    branches: [main]

jobs:
  review:
    uses: emilgruzalski/gh-reusable-workflows/.github/workflows/dependency-review.yml@v1
    with:
      fail-on-severity: 'high'
      deny-licenses: 'GPL-3.0,AGPL-3.0'
    permissions:
      contents: read
      pull-requests: write
```

### Secret Scanning

```yaml
name: Security

on:
  push:
  pull_request:
  schedule:
    - cron: '0 0 * * 0'  # Weekly

jobs:
  secrets:
    uses: emilgruzalski/gh-reusable-workflows/.github/workflows/secret-scan.yml@v1
    permissions:
      contents: read
      security-events: write
```

## Documentation

Each composite action includes comprehensive documentation:

- **[Go Code Quality](/.github/actions/cq-go/README.md)** - Setup, testing, and linting for Go
- **[Node.js Code Quality](/.github/actions/cq-node/README.md)** - Bun-powered Node.js workflows
- **[Python Code Quality](/.github/actions/cq-py/README.md)** - Modern Python with uv and Ruff
- **[Build Docker Images](/.github/actions/build-image/README.md)** - Multi-arch container builds
- **[Security Scanning](/.github/actions/scan-image/README.md)** - Trivy vulnerability scanning
- **[Image Signing](/.github/actions/sign-image/README.md)** - Sigstore keyless signing
- **[SBOM Generation](/.github/actions/sbom/README.md)** - Software Bill of Materials

## Security

This project implements security best practices:

- ✅ All GitHub Actions pinned to full commit SHAs
- ✅ Vulnerability scanning with Trivy
- ✅ Secret detection with TruffleHog
- ✅ Container image signing with Sigstore
- ✅ SBOM generation with CycloneDX
- ✅ Dependency review on pull requests
- ✅ Minimal permissions following principle of least privilege

## What's Included

### 🏗️ CI Pipelines

Each language-specific CI workflow includes:

1. **Code Quality Checks**
   - Linting (language-specific)
   - Code formatting validation
   - Unit tests with coverage
   - Static analysis

2. **Security Scanning**
   - Dependency vulnerability scanning (Trivy)
   - Secret detection (TruffleHog)
   - SBOM generation (CycloneDX)

3. **Container Build & Publish**
   - Multi-architecture builds (AMD64, ARM64)
   - Automatic tagging (SHA, branch, semver)
   - Push to GitHub Container Registry
   - Keyless signing with Sigstore

4. **Observability**
   - Test coverage reports
   - Job summaries with key metrics
   - SARIF upload to Security tab

### ⚙️ Configuration

All workflows support customization through inputs:

- Working directory (monorepo support)
- Version file location
- Dockerfile path and build context
- Security scan severity levels
- Push conditions

## Versioning

This project follows [Semantic Versioning](https://semver.org/).

- **v1.x** - Current stable version
- **v1** - Tracks latest v1.x release (recommended for production)
- **main** - Development branch (not recommended for production use)

See [CHANGELOG.md](CHANGELOG.md) for version history.

## Contributing

Contributions are welcome!

### Development

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Make your changes
4. Test with a sample project
5. Commit your changes (`git commit -m 'Add amazing feature'`)
6. Push to the branch (`git push origin feature/amazing-feature`)
7. Open a Pull Request

## Support

- 📝 **Documentation**: See individual action README files
- 🐛 **Issues**: [GitHub Issues](https://github.com/emilgruzalski/gh-reusable-workflows/issues)
- 💬 **Discussions**: [GitHub Discussions](https://github.com/emilgruzalski/gh-reusable-workflows/discussions)

## Related Projects

- [GitHub Actions Documentation](https://docs.github.com/en/actions)
- [Sigstore](https://www.sigstore.dev/)
- [Trivy](https://aquasecurity.github.io/trivy/)
- [CycloneDX](https://cyclonedx.org/)

## License

This project is licensed under the Apache License 2.0. See [LICENSE](LICENSE) for details.
