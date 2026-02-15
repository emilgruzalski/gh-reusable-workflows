# Go Code Quality Action

Runs linting and tests for Go projects with automatic coverage reporting.

## Features

- **Go Setup**: Automatically installs the correct Go version from `go.mod`
- **Dependency Caching**: Uses GitHub Actions cache for faster builds
- **Vet**: Runs `go vet` to catch common Go mistakes
- **Linting**: Uses golangci-lint for comprehensive code analysis
- **Testing**: Runs tests with race detection and generates coverage reports
- **Coverage Output**: Exports test coverage as an action output

## Usage

```yaml
- name: Code quality
  uses: emilgruzalski/gh-reusable-workflows/.github/actions/cq-go@v1
  with:
    go-version-file: 'go.mod'  # optional, default: go.mod
    working-directory: '.'      # optional, default: .
```

## Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `go-version-file` | Path to the file containing Go version (go.mod or .go-version) | No | `go.mod` |
| `working-directory` | Directory containing the Go module to test and lint | No | `.` |

## Outputs

| Output | Description |
|--------|-------------|
| `coverage` | Test coverage percentage (e.g., "85.2%") |

## Example with Coverage

```yaml
- name: Code quality
  id: quality
  uses: emilgruzalski/gh-reusable-workflows/.github/actions/cq-go@v1

- name: Check coverage
  run: |
    echo "Coverage: ${{ steps.quality.outputs.coverage }}"
```

## Prerequisites

- Go project with `go.mod` file
- `.golangci.yml` or `.golangci.yaml` for linter configuration (optional)

## What It Does

1. Installs Go based on the version specified in `go-version-file`
2. Downloads Go module dependencies
3. Runs `go vet` to check for common mistakes
4. Runs golangci-lint with your project configuration
5. Runs tests with `-race` flag and generates coverage report
6. Outputs coverage percentage for use in later steps
