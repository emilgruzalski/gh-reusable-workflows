# Python Code Quality Action

Runs linting, formatting checks, and tests for Python projects using uv package manager.

## Features

- **Python Setup**: Automatically installs the correct Python version from `pyproject.toml`
- **uv Package Manager**: Uses Astral's uv for extremely fast dependency management
- **Dependency Caching**: Caches Python packages for faster builds
- **Linting**: Runs Ruff linter for code quality checks
- **Format Checking**: Verifies code formatting with Ruff formatter
- **Testing**: Executes pytest test suite
- **Job Summaries**: Adds test status to GitHub Actions summary

## Usage

```yaml
- name: Code quality
  uses: emilgruzalski/gh-reusable-workflows/.github/actions/cq-py@v1
  with:
    python-version-file: 'pyproject.toml'  # optional, default: pyproject.toml
    working-directory: '.'                  # optional, default: .
```

## Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `python-version-file` | Path to file containing Python version configuration (pyproject.toml or .python-version) | No | `pyproject.toml` |
| `working-directory` | Directory containing the Python project to test, lint, and format check | No | `.` |

## Outputs

| Output | Description |
|--------|-------------|
| `test-result` | Test execution result (`success` or `failure`) |

## Prerequisites

- Python project with `pyproject.toml`
- `pyproject.toml` configured for uv (with `[project]` section)
- Ruff configured for linting and formatting
- pytest installed as a dev dependency

## What It Does

1. Installs uv package manager with caching enabled
2. Installs Python based on the version in `python-version-file`
3. Syncs dependencies with `uv sync`
4. Runs `uv run ruff check .` for linting
5. Runs `uv run ruff format --check .` for format verification
6. Runs `uv run pytest` for testing
7. Adds test summary to GitHub Actions

## Example

```yaml
- name: Code quality
  id: quality
  uses: emilgruzalski/gh-reusable-workflows/.github/actions/cq-py@v1

- name: Check test result
  if: steps.quality.outputs.test-result == 'failure'
  run: exit 1
```

## Configuration

### Example `pyproject.toml`

```toml
[project]
name = "my-project"
version = "0.1.0"
requires-python = ">=3.11"
dependencies = [
    "fastapi>=0.100.0",
]

[tool.uv]
dev-dependencies = [
    "pytest>=7.0.0",
    "ruff>=0.1.0",
]

[tool.ruff]
line-length = 100
```
