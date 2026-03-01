# Java Code Quality Action

Runs compilation and tests for Java projects using Maven.

## Features

- **Java Setup**: Automatically installs the specified Java version (Temurin distribution)
- **Dependency Caching**: Uses Maven dependency cache for faster builds
- **Compilation**: Runs `mvn compile` to verify the project compiles
- **Testing**: Executes test suite with `mvn test`
- **Job Summaries**: Adds test status to GitHub Actions summary

## Usage

```yaml
- name: Code quality
  uses: emilgruzalski/gh-reusable-workflows/.github/actions/cq-java@v1
  with:
    java-version: '21'  # optional, default: 21
    working-directory: '.'  # optional, default: .
```

## Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `java-version` | Java version to install (e.g., 17, 21). Uses Temurin distribution. | No | `21` |
| `working-directory` | Directory containing the Maven project to compile and test | No | `.` |

## Outputs

| Output | Description |
|--------|-------------|
| `test-result` | Test execution result (`success` or `failure`) |

## Prerequisites

- Java project with `pom.xml`
- Maven wrapper (`mvnw`) is not required — system Maven is used

## What It Does

1. Installs Java (Temurin) based on the `java-version` input
2. Configures Maven dependency caching from `pom.xml`
3. Runs `mvn -B compile` to compile the project
4. Runs `mvn -B test` to execute the test suite
5. Adds test summary to GitHub Actions

## Example

```yaml
- name: Code quality
  id: quality
  uses: emilgruzalski/gh-reusable-workflows/.github/actions/cq-java@v1
  with:
    java-version: '17'
    working-directory: './api'

- name: Check test result
  if: steps.quality.outputs.test-result == 'success'
  run: echo "Tests passed!"
```
