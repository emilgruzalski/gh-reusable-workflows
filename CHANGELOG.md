# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [0.1.0] - 2026-02-10

### Added

- "build-image" composite action for building and pushing Docker images to ghcr.io.
- "cq-node" composite action for Node.js code quality using Bun.
- "cq-go" composite action for Go code quality (vet, lint, test).
- "cq-py" composite action for Python code quality using uv and ruff.
- "ci-go" reusable workflow.
- "ci-node" reusable workflow.
- "ci-py" reusable workflow.

## [0.0.3] - 2025-03-02

### Changed

- "cd-az-swa" workflow to "cd-az-stapp".
- abbreviate contents of CODEOWNERS file.
  
## [0.0.2] - 2025-01-26

### Added

- CODEOWNERS file.
- This CHANGELOG file.

### Changed

- "azure-staticwebapp" workflow to "cd-az-swa".

## [0.0.1] - 2024-11-20

### Added

- "azure-staticwebapp" workflow.
