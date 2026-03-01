# .NET Code Quality Action

Runs restore, build, format check, and tests for .NET/C# projects.

## Features

- **.NET Setup**: Automatically installs the correct .NET SDK version from `global.json`
- **Dependency Restore**: Runs `dotnet restore` for NuGet packages
- **Build**: Compiles the project with `dotnet build`
- **Format Check**: Verifies code formatting with `dotnet format`
- **Testing**: Executes test suite with `dotnet test`
- **Job Summaries**: Adds test status to GitHub Actions summary

## Usage

```yaml
- name: Code quality
  uses: emilgruzalski/gh-reusable-workflows/.github/actions/cq-dotnet@v1
  with:
    global-json-file: 'global.json'  # optional, default: global.json
    working-directory: '.'            # optional, default: .
```

## Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `global-json-file` | Path to `global.json` file for .NET SDK version pinning | No | `global.json` |
| `working-directory` | Directory containing the .NET project or solution to build and test | No | `.` |

## Outputs

| Output | Description |
|--------|-------------|
| `test-result` | Test execution result (`success` or `failure`) |

## Prerequisites

- .NET project with `.csproj` or `.sln` file
- `global.json` for SDK version pinning (recommended)
- (Optional) `.editorconfig` for formatting rules

## What It Does

1. Installs .NET SDK based on the version in `global-json-file`
2. Runs `dotnet restore` to restore NuGet packages
3. Runs `dotnet build --no-restore` to compile the project
4. Runs `dotnet format --verify-no-changes --no-restore` to check code formatting
5. Runs `dotnet test --no-build` to execute the test suite
6. Adds test summary to GitHub Actions

## Example

```yaml
- name: Code quality
  id: quality
  uses: emilgruzalski/gh-reusable-workflows/.github/actions/cq-dotnet@v1
  with:
    working-directory: './src'

- name: Check test result
  if: steps.quality.outputs.test-result == 'success'
  run: echo "Tests passed!"
```

## Configuration

### Example `global.json`

```json
{
  "sdk": {
    "version": "9.0.100",
    "rollForward": "latestFeature"
  }
}
```
