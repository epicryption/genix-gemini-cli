# Multi-Ecosystem Packaging & Integration Engine

[![CycloneDX SBOM Generation](https://github.com/epicryption/genix-gemini-cli/actions/workflows/sbom.yml/badge.svg)](https://github.com/epicryption/genix-gemini-cli/actions/workflows/sbom.yml)
[![License](https://img.shields.io/github/license/epicryption/genix-gemini-cli)](LICENSE)
[![GitHub Stars](https://img.shields.io/github/stars/epicryption/genix-gemini-cli?style=social)](https://github.com/epicryption/genix-gemini-cli)

**Genix Gemini CLI** is a multi-ecosystem packaging and integration engine designed to manage software bills of materials (SBOMs), cross-ecosystem dependencies, and enterprise automation workflows seamlessly.

---

## 🛠️ GitHub Actions CI/CD Workflow

The project automatically generates **CycloneDX SBOMs** across multiple package ecosystems (`npm`, `maven`, `pypi`, and `go`) on every push or pull request to the `main` or `master` branches.

### Workflow Configuration (`.github/workflows/sbom.yml`)

```yaml
name: Generate CycloneDX SBOM

on:
  push:
    branches: [ main, master ]
  pull_request:
    branches: [ main, master ]

jobs:
  generate-sbom:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        ecosystem: [npm, maven, pypi, go]
    steps:
      - name: Checkout Repository
        uses: actions/checkout@v4

      - name: Setup Node.js (for npm / frontend libs)
        if: matrix.ecosystem == 'npm'
        uses: actions/setup-node@v4
        with:
          node-version: '20'

      - name: Setup Java (for Maven)
        if: matrix.ecosystem == 'maven'
        uses: actions/setup-java@v4
        with:
          distribution: 'temurin'
          java-version: '17'

      - name: Setup Python (for PyPI)
        if: matrix.ecosystem == 'pypi'
        uses: actions/setup-python@v5
        with:
          python-version: '3.11'

      - name: Setup Go
        if: matrix.ecosystem == 'go'
        uses: actions/setup-go@v5
        with:
          go-version: '1.22'

      - name: Install cdxgen CLI
        run: npm install -g @cyclonedx/cdxgen

      - name: Generate SBOM for ${{ matrix.ecosystem }}
        run: |
          cdxgen -t ${{ matrix.ecosystem }} --output-file bom-${{ matrix.ecosystem }}.xml

      - name: Upload SBOM Artifact
        uses: actions/upload-artifact@v4
        with:
          name: bom-${{ matrix.ecosystem }}-xml
          path: bom-${{ matrix.ecosystem }}.xml
