# Generate SBOM Action

Generates a Software Bill of Materials (SBOM) using cdxgen with optional Dependency Track integration.

## Features

- **CycloneDX Format**: Generates standard CycloneDX SBOM in JSON format
- **Multi-Language**: Supports Go, Node.js, Python, Java, and many more
- **Dependency Track**: Optional automatic upload to Dependency Track server
- **Artifact Upload**: Automatically uploads SBOM as GitHub Actions artifact
- **Fast Execution**: Uses Bun for rapid cdxgen installation
- **Job Summaries**: Adds SBOM generation status to GitHub Actions summary

## Usage

### Basic Usage (Generate and Upload as Artifact)

```yaml
- name: Generate SBOM
  uses: emilgruzalski/gh-reusable-workflows/.github/actions/sbom@v1
```

### With Dependency Track Upload

```yaml
- name: Generate and upload SBOM
  uses: emilgruzalski/gh-reusable-workflows/.github/actions/sbom@v1
  with:
    server-url: 'https://dtrack.example.com'
    api-key: ${{ secrets.DEPENDENCY_TRACK_API_KEY }}
```

## Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `working-directory` | Directory containing source code to scan for dependencies | No | `.` |
| `output` | Output filename for the generated SBOM in CycloneDX JSON format | No | `bom.json` |
| `artifact-name` | Name for the GitHub Actions artifact containing the SBOM | No | `sbom` |
| `server-url` | Dependency Track server URL for automatic SBOM upload | No | `""` |
| `api-key` | Dependency Track API key for authentication | No | `""` |

## What It Does

1. Installs Node.js and Bun package manager
2. Installs cdxgen globally using Bun
3. Generates SBOM by scanning the working directory recursively
4. **If Dependency Track is configured**: Uploads SBOM to the server
5. Uploads SBOM as a GitHub Actions artifact
6. Adds generation summary to GitHub Actions

## Examples

### Multi-Project Monorepo

```yaml
- name: Generate SBOM for backend
  uses: emilgruzalski/gh-reusable-workflows/.github/actions/sbom@v1
  with:
    working-directory: './backend'
    output: 'backend-bom.json'
    artifact-name: 'backend-sbom'

- name: Generate SBOM for frontend
  uses: emilgruzalski/gh-reusable-workflows/.github/actions/sbom@v1
  with:
    working-directory: './frontend'
    output: 'frontend-bom.json'
    artifact-name: 'frontend-sbom'
```

### Custom Output Location

```yaml
- name: Generate SBOM
  uses: emilgruzalski/gh-reusable-workflows/.github/actions/sbom@v1
  with:
    output: 'sbom/cyclonedx.json'
    artifact-name: 'software-bill-of-materials'
```

## Supported Languages and Ecosystems

cdxgen supports:

- **JavaScript/TypeScript**: npm, yarn, pnpm, bun
- **Python**: pip, poetry, pipenv
- **Go**: go.mod
- **Java**: Maven, Gradle
- **.NET**: NuGet
- **Ruby**: Bundler
- **PHP**: Composer
- **Rust**: Cargo
- And many more...

## SBOM Output

The generated SBOM includes:

- All direct and transitive dependencies
- Component versions and licenses
- Dependency relationships
- Package URLs (purl)
- CycloneDX metadata

### Example SBOM Structure

```json
{
  "bomFormat": "CycloneDX",
  "specVersion": "1.5",
  "version": 1,
  "components": [
    {
      "type": "library",
      "name": "express",
      "version": "4.18.2",
      "purl": "pkg:npm/express@4.18.2"
    }
  ]
}
```

## Integration with Dependency Track

[Dependency Track](https://dependencytrack.org/) is an open-source SBOM analysis platform that:

- Tracks known vulnerabilities
- Monitors license compliance
- Provides risk scoring
- Enables continuous monitoring

To use with Dependency Track:

1. Deploy Dependency Track server
2. Create an API key in Dependency Track
3. Add API key as GitHub secret
4. Configure this action with `server-url` and `api-key`

## Downloading the SBOM Artifact

The SBOM is automatically uploaded as a workflow artifact. To download it:

1. Go to the workflow run in GitHub Actions
2. Scroll to the "Artifacts" section
3. Click on the artifact name (default: `sbom`)
4. The SBOM JSON file will be downloaded

Alternatively, use the GitHub CLI:

```bash
gh run download <run-id> -n sbom
```
