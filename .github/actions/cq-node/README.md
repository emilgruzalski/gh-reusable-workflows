# Node.js Code Quality Action

Runs lint, tests, and build for Node.js projects using Bun package manager.

## Features

- **Node.js Setup**: Automatically installs the correct Node version from `package.json`
- **Bun Runtime**: Uses Bun for fast package installation and test execution
- **Dependency Caching**: Caches npm/Bun dependencies for faster builds
- **Linting**: Runs your configured linter (ESLint, etc.)
- **Testing**: Executes tests with Bun test runner
- **Build**: Runs the build script to verify compilation
- **Job Summaries**: Adds test and build status to GitHub Actions summary

## Usage

```yaml
- name: Code quality
  uses: emilgruzalski/gh-reusable-workflows/.github/actions/cq-node@v1
  with:
    node-version-file: 'package.json'  # optional, default: package.json
    working-directory: '.'              # optional, default: .
```

## Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `node-version-file` | Path to file containing Node.js version (package.json, .nvmrc, or .node-version) | No | `package.json` |
| `working-directory` | Directory containing the Node.js project to test, lint, and build | No | `.` |

## Outputs

| Output | Description |
|--------|-------------|
| `test-result` | Test execution result (`success` or `failure`) |

## Prerequisites

- Node.js project with `package.json`
- `package.json` must include these scripts:
  - `lint`: Your linting command (e.g., `eslint .`)
  - `test`: Your test command
  - `build`: Your build command (e.g., `tsc`, `vite build`)
- Lock file (`bun.lockb`, `package-lock.json`, or `yarn.lock`)

## What It Does

1. Installs Node.js based on the version in `node-version-file`
2. Installs Bun package manager
3. Installs dependencies with `bun install --frozen-lockfile`
4. Runs `bun run lint`
5. Runs `bun test`
6. Runs `bun run build`
7. Adds test and build summaries to GitHub Actions

## Example

```yaml
- name: Code quality
  id: quality
  uses: emilgruzalski/gh-reusable-workflows/.github/actions/cq-node@v1
  with:
    working-directory: './frontend'

- name: Check test result
  if: steps.quality.outputs.test-result == 'success'
  run: echo "Tests passed!"
```
