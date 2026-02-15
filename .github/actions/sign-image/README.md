# Sign Container Image Action

Signs container images using Cosign with keyless signing via Sigstore.

## Features

- **Keyless Signing**: Uses Sigstore for keyless container image signing
- **OIDC Integration**: Leverages GitHub's OIDC provider for authentication
- **Transparency Log**: Signatures are recorded in Sigstore's transparency log (Rekor)
- **Verification**: Signed images can be verified by anyone
- **No Key Management**: No need to manage private keys or secrets
- **Job Summaries**: Adds signing status to GitHub Actions summary

## Usage

```yaml
- name: Sign image
  uses: emilgruzalski/gh-reusable-workflows/.github/actions/sign-image@v1
  with:
    image: ${{ steps.build.outputs.image }}
    digest: ${{ steps.build.outputs.digest }}
```

## Inputs

| Input | Description | Required |
|-------|-------------|----------|
| `image` | Full container image reference including registry and tag | **Yes** |
| `digest` | SHA256 digest of the image to sign | **Yes** |

## Prerequisites

### Required Workflow Permissions

```yaml
permissions:
  id-token: write  # Required for OIDC/Sigstore signing
  packages: write  # Required if pushing signatures to registry
```

### Environment

The workflow must run in an environment where:
- GitHub OIDC is available (public repos, or GitHub Enterprise with OIDC enabled)
- The image has already been built and pushed to a registry

## Complete Example

```yaml
name: Build and Sign

on:
  push:
    branches: [main]

jobs:
  build-and-sign:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      packages: write
      id-token: write  # Required for Sigstore

    steps:
      - uses: actions/checkout@v6

      - name: Build image
        id: build
        uses: emilgruzalski/gh-reusable-workflows/.github/actions/build-image@v1
        with:
          image-name: 'my-app'
          github-token: ${{ github.token }}

      - name: Sign image
        uses: emilgruzalski/gh-reusable-workflows/.github/actions/sign-image@v1
        with:
          image: ${{ steps.build.outputs.image }}
          digest: ${{ steps.build.outputs.digest }}
```

## How It Works

### Keyless Signing with Sigstore

1. **OIDC Token**: GitHub Actions provides an OIDC token proving the workflow identity
2. **Fulcio Certificate**: Sigstore's Fulcio CA issues a short-lived certificate
3. **Signing**: Cosign signs the image using the certificate
4. **Transparency**: Signature is recorded in Rekor (Sigstore's transparency log)
5. **No Secrets**: No private keys are stored or managed

### What Gets Signed

The action signs:
- The exact image digest (immutable reference)
- Metadata about the build (commit SHA, workflow, etc.)

## Verifying Signatures

Anyone can verify your signed images:

```bash
# Verify the signature
cosign verify \
  --certificate-identity=https://github.com/<owner>/<repo>/.github/workflows/<workflow>.yml@refs/heads/main \
  --certificate-oidc-issuer=https://token.actions.githubusercontent.com \
  ghcr.io/<owner>/<image>@sha256:<digest>
```

Or using a simpler approach for GitHub Actions:

```bash
cosign verify \
  --certificate-oidc-issuer=https://token.actions.githubusercontent.com \
  ghcr.io/<owner>/<image>:<tag>
```

## Verification in Kubernetes

Use Sigstore Policy Controller or Kyverno to enforce signature verification:

### Example Kyverno Policy

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: verify-images
spec:
  validationFailureAction: enforce
  rules:
    - name: verify-signature
      match:
        any:
          - resources:
              kinds:
                - Pod
      verifyImages:
        - imageReferences:
            - "ghcr.io/<owner>/*"
          attestors:
            - entries:
                - keyless:
                    rekor:
                      url: https://rekor.sigstore.dev
                    issuer: https://token.actions.githubusercontent.com
                    subject: https://github.com/<owner>/<repo>/*
```

## Benefits

### Security
- **Supply Chain Protection**: Ensures images are from trusted sources
- **Tamper Detection**: Any modification invalidates the signature
- **Audit Trail**: Transparency log provides permanent record

### Compliance
- **SLSA Provenance**: Supports SLSA Level 3 requirements
- **Non-Repudiation**: Signatures prove image origin
- **Verifiable**: Anyone can verify signatures

## Troubleshooting

### "failed to sign: getting signer: getting cert: retrieving cert: the token is not valid"

**Solution**: Ensure `id-token: write` permission is set in the workflow.

### "COSIGN_EXPERIMENTAL=true is required"

**Solution**: This is handled automatically by the action. No action needed.

### Signature not found during verification

**Possible causes**:
1. Image was rebuilt/retagged after signing (digest changed)
2. Signature was signed with different OIDC identity
3. Using wrong certificate identity in verification command

## Learn More

- [Sigstore Documentation](https://docs.sigstore.dev/)
- [Cosign GitHub](https://github.com/sigstore/cosign)
- [Keyless Signing Guide](https://github.com/sigstore/cosign/blob/main/KEYLESS.md)
- [SLSA Framework](https://slsa.dev/)
