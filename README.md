# GitHub Reusable Workflows

[![License: Apache 2.0](https://img.shields.io/badge/License-Apache_2.0-blue.svg)](LICENSE)

Production-ready, security-focused reusable GitHub Actions workflows and composite actions for Go, Node.js, and Python projects.

## Overview

This repository provides a collection of enterprise-grade, reusable GitHub Actions workflows and composite actions that implement modern DevOps best practices including:

- 🚀 **Multi-Language Support** - Purpose-built workflows for Go, Node.js, and Python with language-specific optimizations
- 📦 **Complete CI/CD Pipeline** - Code quality checks, testing, container builds, and deployment
- ⚡ **Performance Optimized** - Intelligent caching, parallel execution, and efficient resource utilization
- 📊 **Observability** - Rich job summaries and coverage reports

## Features

### 🔧 Composite Actions

| Action | Description | Languages |
|--------|-------------|-----------|
| [cq-go](/.github/actions/cq-go) | Go code quality: vet, lint (golangci-lint), test with coverage | Go |
| [cq-node](/.github/actions/cq-node) | Node.js code quality: lint, test, build using Bun | Node.js |
| [cq-py](/.github/actions/cq-py) | Python code quality: lint (Ruff), format check, test | Python |
| [build-image](/.github/actions/build-image) | Multi-arch Docker image builds with GHCR push | All |

### 🔄 Reusable Workflows

| Workflow | Description | Use Case |
|----------|-------------|----------|
| [ci-go.yml](/.github/workflows/ci-go.yml) | Go CI pipeline with quality checks and image build | Go services |
| [ci-node.yml](/.github/workflows/ci-node.yml) | Node.js CI pipeline with Bun and image build | Node.js apps |
| [ci-py.yml](/.github/workflows/ci-py.yml) | Python CI pipeline with uv and image build | Python apps |
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
```

#### For Azure Static Web Apps

```yaml
name: CD - Azure Static Web Apps

on:
  push:
    branches: [main]
  pull_request:
    types: [opened, synchronize, reopened, closed]
    branches: [main]

jobs:
  cd:
    uses: emilgruzalski/gh-reusable-workflows/.github/workflows/cd-az-stapp.yml@v1
    with:
      app-location: '/'
      api-location: 'api'
      app-artifact-location: 'build'
    secrets:
      azure-static-web-apps-api-token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN }}
      gh-token: ${{ secrets.GITHUB_TOKEN }}
    permissions:
      contents: read
      pull-requests: write
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
```

## Documentation

Each composite action includes comprehensive documentation:

- **[Go Code Quality](/.github/actions/cq-go/README.md)** - Setup, testing, and linting for Go
- **[Node.js Code Quality](/.github/actions/cq-node/README.md)** - Bun-powered Node.js workflows
- **[Python Code Quality](/.github/actions/cq-py/README.md)** - Modern Python with uv and Ruff
- **[Build Docker Images](/.github/actions/build-image/README.md)** - Multi-arch container builds

## What's Included

### 🏗️ CI Pipelines

Each language-specific CI workflow includes:

1. **Code Quality Checks**
   - Linting (language-specific)
   - Code formatting validation
   - Unit tests with coverage
   - Static analysis

2. **Container Build & Publish**
   - Multi-architecture builds (AMD64, ARM64)
   - Automatic tagging (SHA, branch, semver)
   - Push to GitHub Container Registry

3. **Observability**
   - Test coverage reports
   - Job summaries with key metrics

### 🌐 CD Pipelines

- **Azure Static Web Apps** - Build and deploy frontend apps with PR preview environments

### ⚙️ Configuration

All workflows support customization through inputs:

- Working directory (monorepo support)
- Version file location
- Dockerfile path and build context
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

## License

This project is licensed under the Apache License 2.0. See [LICENSE](LICENSE) for details.
