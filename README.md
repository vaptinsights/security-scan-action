# VAPT Insights Security Scan

Generate a CycloneDX Software Bill of Materials (SBOM) with Trivy and upload it to VAPT Insights.

## Features

- Trivy vulnerability scanning
- CycloneDX SBOM generation
- Whole-repository scanning
- Nested-folder scanning
- Monorepo service support
- Automatic commit-based artifact versions

## Prerequisites

Create a VAPT Insights API key and save it in your repository as:

```text
VAPT_INSIGHTS_API_KEY
```

Go to:

**Repository Settings → Secrets and variables → Actions → New repository secret**

## Basic usage

Create `.github/workflows/vapt-security-scan.yml`:

```yaml
name: VAPT Insights Security Scan

on:
  push:
    branches: [main]
  workflow_dispatch:

jobs:
  security-scan:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Run VAPT Insights scan
        uses: vaptinsights/security-scan-action@v1
        with:
          api-key: ${{ secrets.VAPT_INSIGHTS_API_KEY }}
          scan-path: .
          artifact-name: ${{ github.event.repository.name }}
```

## Nested-folder usage

```yaml
- name: Scan UI application
  uses: vaptinsights/security-scan-action@v1
  with:
    api-key: ${{ secrets.VAPT_INSIGHTS_API_KEY }}
    scan-path: ui
    artifact-name: ${{ github.event.repository.name }}/ui
```

## Monorepo usage

```yaml
jobs:
  security-scan:
    runs-on: ubuntu-latest

    strategy:
      fail-fast: false
      matrix:
        service:
          - name: ui
            path: ui
          - name: sms-service
            path: sms-service
          - name: api-service
            path: api-service

    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Scan ${{ matrix.service.name }}
        uses: vaptinsights/security-scan-action@v1
        with:
          api-key: ${{ secrets.VAPT_INSIGHTS_API_KEY }}
          scan-path: ${{ matrix.service.path }}
          artifact-name: ${{ github.event.repository.name }}/${{ matrix.service.name }}
```

Each service is scanned independently and uploaded as a separate VAPT Insights artifact.

## Inputs

| Input | Required | Default | Description |
|---|---:|---|---|
| `api-key` | Yes | — | VAPT Insights API key |
| `artifact-name` | Yes | — | Artifact name shown in VAPT Insights |
| `scan-path` | No | `.` | Repository folder to scan |
| `artifact-version` | No | Short commit SHA | Artifact version |

## Security

Never place your API key directly in workflow YAML. Always use GitHub Actions secrets.

To report a security issue, follow the instructions in `SECURITY.md`.

## License

Distributed under the MIT License.
