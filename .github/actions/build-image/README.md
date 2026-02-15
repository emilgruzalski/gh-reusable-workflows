# Build and Push Docker Image Action

Builds Docker images with multi-architecture support and pushes them to GitHub Container Registry (ghcr.io).

## Features

- **Multi-Architecture**: Supports ARM64 and AMD64 by default
- **Automatic Tagging**: Tags images with SHA, branch, PR number, and semver
- **GitHub Container Registry**: Pushes to ghcr.io automatically
- **Build Caching**: Uses GitHub Actions cache for layer caching
- **Input Validation**: Validates image names before building
- **Job Summaries**: Adds build details to GitHub Actions summary

## Usage

```yaml
- name: Build and push Docker image
  id: build
  uses: emilgruzalski/gh-reusable-workflows/.github/actions/build-image@v1
  with:
    image-name: 'my-app'
    github-token: ${{ github.token }}
```

## Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `image-name` | Name of the Docker image (without registry prefix) | **Yes** | - |
| `github-token` | GitHub token for authenticating to ghcr.io | **Yes** | - |
| `dockerfile` | Path to the Dockerfile relative to the build context | No | `./Dockerfile` |
| `context` | Docker build context directory | No | `.` |
| `platforms` | Comma-separated list of target platforms for multi-architecture builds | No | `linux/amd64,linux/arm64` |
| `push` | Whether to push the built image to the registry | No | `true` |
| `build-args` | Docker build arguments, one per line | No | `""` |

## Outputs

| Output | Description |
|--------|-------------|
| `image` | Full image reference with SHA tag (e.g., `ghcr.io/owner/image:sha-abc1234`) |
| `digest` | Image digest (e.g., `sha256:abc123...`) |

## Examples

### Basic Usage

```yaml
- uses: actions/checkout@v6

- name: Build and push
  uses: emilgruzalski/gh-reusable-workflows/.github/actions/build-image@v1
  with:
    image-name: 'my-api'
    github-token: ${{ github.token }}
```

### With Build Arguments

```yaml
- name: Build with args
  uses: emilgruzalski/gh-reusable-workflows/.github/actions/build-image@v1
  with:
    image-name: 'my-app'
    github-token: ${{ github.token }}
    build-args: |
      NODE_ENV=production
      API_URL=https://api.example.com
```

### Custom Dockerfile Location

```yaml
- name: Build from custom Dockerfile
  uses: emilgruzalski/gh-reusable-workflows/.github/actions/build-image@v1
  with:
    image-name: 'my-service'
    dockerfile: './docker/Dockerfile.prod'
    context: '.'
    github-token: ${{ github.token }}
```

### Single Platform Build

```yaml
- name: Build AMD64 only
  uses: emilgruzalski/gh-reusable-workflows/.github/actions/build-image@v1
  with:
    image-name: 'my-app'
    platforms: 'linux/amd64'
    github-token: ${{ github.token }}
```

### Build Without Pushing (Testing)

```yaml
- name: Test build
  uses: emilgruzalski/gh-reusable-workflows/.github/actions/build-image@v1
  with:
    image-name: 'my-app'
    push: 'false'
    github-token: ${{ github.token }}
```

## Image Tags

The action automatically tags images with:

- `sha-<commit>`: Git commit SHA (short)
- `<branch-name>`: Current branch (for pushes)
- `pr-<number>`: PR number (for pull requests)
- `<version>`: Semver tags (if pushed with git tag)
- `<major>.<minor>`: Semver major.minor
- `latest`: On default branch only

Example output:
```
ghcr.io/owner/my-app:sha-abc1234
ghcr.io/owner/my-app:main
ghcr.io/owner/my-app:latest
```

## Prerequisites

- Dockerfile in your repository
- `packages: write` permission in your workflow

## Required Permissions

```yaml
permissions:
  packages: write  # For pushing to ghcr.io
```
