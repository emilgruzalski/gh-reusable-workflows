# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.1.0] - 2026-02-15

### Added

- Comprehensive README.md files for all 7 composite actions with usage examples, inputs/outputs documentation, and troubleshooting guides.
- Author metadata (`author: 'Emil Gruzalski'`) to all composite actions.
- Branding (icons and colors) to all composite actions for improved GitHub Marketplace presentation.
- Coverage output to "cq-go" action (`coverage` output with test coverage percentage).
- Test result outputs to "cq-node" and "cq-py" actions (`test-result` output).
- Input validation in "build-image" action (validates image name format before building).
- Job summaries using `$GITHUB_STEP_SUMMARY` in all composite actions for improved visibility.
- Timeout protection to all workflow jobs (15-30 minutes depending on job type).
- Inline permission documentation explaining why each permission is needed in all workflows.
- "dependency-review" reusable workflow for PR-based dependency vulnerability scanning.
- Workflow templates in `.github/workflow-templates/` for easy adoption (5 templates: ci-go, ci-node, ci-python, dependency-review, secret-scan).
- Caching to setup steps (Go, Node.js, Python) for faster builds.

### Changed

- Pinned all GitHub Actions to full 40-character commit SHAs instead of version tags for supply chain security (18 actions updated).
- Enhanced all input descriptions with detailed explanations, examples, and default behavior documentation.
- Improved concurrency control to use PR numbers and only cancel PR builds, not push builds.
- Optimized "sbom" action to eliminate duplicate cdxgen execution when uploading to Dependency Track.

### Fixed

- "sbom" action now runs cdxgen only once (previously ran twice when server-url was provided).

## [1.0.0] - 2026-02-11

### Added

- "scan-image" composite action for vulnerability scanning using Trivy (filesystem mode, SARIF upload to GitHub Security).
- "sign-image" composite action for keyless container image signing using Cosign/Sigstore.
- "secret-scan" reusable workflow for secret scanning using TruffleHog.
- Dependabot configuration for automated GitHub Actions updates.
- QEMU setup in "build-image" action for multi-arch builds on GitHub-hosted runners.
- Workflow outputs (`image`, `digest`) in all CI workflows for downstream CD integration.
- Concurrency control with `cancel-in-progress` in all CI workflows.
- `id-token: write` and `security-events: write` permissions for Cosign signing and Trivy SARIF upload.

### Changed

- "sbom" composite action now uses CycloneDX cdxgen (installed via Bun) instead of Anchore Syft.
- "sbom" composite action scans source code instead of container images.
- "build-image" action now exports `image` and `digest` outputs.
- Fixed SBOM image tag mismatch (full SHA vs short SHA from docker/metadata-action).

## [0.1.0] - 2026-02-10

### Added

- "build-image" composite action for building and pushing Docker images to ghcr.io.
- "cq-node" composite action for Node.js code quality using Bun.
- "cq-go" composite action for Go code quality (vet, lint, test).
- "cq-py" composite action for Python code quality using uv and ruff.
- "sbom" composite action for generating Software Bill of Materials from container images.
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
