# Rust Code Quality Action

Runs formatting checks, linting (Clippy), and tests for Rust projects.

## Features

- **Rust Setup**: Automatically installs the correct Rust toolchain with required components
- **Dependency Caching**: Uses GitHub Actions cache for faster builds
- **Format Check**: Runs `cargo fmt --check` to verify code formatting
- **Linting**: Uses Clippy for comprehensive code analysis with warnings as errors
- **Testing**: Runs full test suite with `cargo test`
- **Job Summaries**: Adds test status to GitHub Actions summary

## Usage

```yaml
- name: Code quality
  uses: emilgruzalski/gh-reusable-workflows/.github/actions/cq-rust@v1
  with:
    toolchain: 'stable'  # optional, default: stable
    working-directory: '.'  # optional, default: .
```

## Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `toolchain` | Rust toolchain to install (e.g., stable, nightly, 1.78). If `rust-toolchain.toml` exists, it takes precedence. | No | `stable` |
| `working-directory` | Directory containing the Rust project to test and lint | No | `.` |

## Outputs

| Output | Description |
|--------|-------------|
| `test-result` | Test execution result (`success` or `failure`) |

## Prerequisites

- Rust project with `Cargo.toml`
- (Optional) `rust-toolchain.toml` for pinning the toolchain version
- (Optional) `clippy.toml` or `.clippy.toml` for Clippy configuration
- (Optional) `rustfmt.toml` or `.rustfmt.toml` for formatting configuration

## What It Does

1. Installs Rust toolchain with `clippy` and `rustfmt` components
2. Runs `cargo fmt --check` to verify code formatting
3. Runs `cargo clippy -- -D warnings` to lint with warnings treated as errors
4. Runs `cargo test` to execute the test suite
5. Adds test summary to GitHub Actions

## Example

```yaml
- name: Code quality
  id: quality
  uses: emilgruzalski/gh-reusable-workflows/.github/actions/cq-rust@v1
  with:
    working-directory: './backend'

- name: Check test result
  if: steps.quality.outputs.test-result == 'success'
  run: echo "Tests passed!"
```
