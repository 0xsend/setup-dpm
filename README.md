# setup-dpm

GitHub Action to install the [Digital Asset Package Manager (dpm)](https://docs.digitalasset.com/build/3.4/dpm/dpm.html) CLI for DAML development.

## Usage

### Basic usage (latest version)

```yaml
steps:
  - uses: actions/checkout@v4

  - name: Setup dpm
    uses: 0xsend/setup-dpm@v1

  - name: Build DAML project
    run: dpm build
```

### Specific version

```yaml
steps:
  - uses: actions/checkout@v4

  - name: Setup dpm
    uses: 0xsend/setup-dpm@v1
    with:
      version: '3.4.9'

  - name: Build DAML project
    run: dpm build
```

### With DAML SDK

```yaml
steps:
  - uses: actions/checkout@v4

  - name: Setup dpm with DAML SDK
    uses: 0xsend/setup-dpm@v1
    with:
      version: '3.4.9'
      daml-version: '3.4.9'

  - name: Build DAML project
    run: dpm build
```

### Using outputs

```yaml
steps:
  - uses: actions/checkout@v4

  - name: Setup dpm
    id: setup-dpm
    uses: 0xsend/setup-dpm@v1

  - name: Show installed version
    run: |
      echo "Installed dpm version: ${{ steps.setup-dpm.outputs.version }}"
      echo "Installation path: ${{ steps.setup-dpm.outputs.path }}"
```

## Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `version` | Version of dpm to install (e.g., `3.4.9` or `latest`) | No | `latest` |
| `daml-version` | Version of DAML SDK to install (e.g., `3.4.9`). Leave empty to skip. | No | `''` |

## Outputs

| Output | Description |
|--------|-------------|
| `version` | The version of dpm that was installed |
| `path` | Path to the dpm installation directory |
| `daml-version` | The version of DAML SDK that was installed (empty if not installed) |

## Supported Platforms

| Platform | Runner |
|----------|--------|
| Linux x86_64 | `ubuntu-latest` |
| Linux ARM64 | `ubuntu-24.04-arm` |
| macOS x86_64 | `macos-15-intel` |
| macOS ARM64 | `macos-14`, `macos-latest` |

## Caching

This action automatically caches dpm installations using `actions/cache`. Subsequent runs with the same version will use the cached installation, significantly reducing setup time.

Cache key format: `dpm-{platform}-{version}`

DAML SDK installations are managed by dpm and stored in its own cache directory.

## Example Workflows

### Build and test DAML project

```yaml
name: CI

on: [push, pull_request]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Setup dpm
        uses: 0xsend/setup-dpm@v1

      - name: Build
        run: dpm build

      - name: Test
        run: dpm test
```

### Matrix build across platforms

```yaml
name: Cross-platform CI

on: [push, pull_request]

jobs:
  build:
    strategy:
      matrix:
        os: [ubuntu-latest, macos-14]
    runs-on: ${{ matrix.os }}

    steps:
      - uses: actions/checkout@v4

      - name: Setup dpm
        uses: 0xsend/setup-dpm@v1

      - name: Build
        run: dpm build

      - name: Test
        run: dpm test
```

### Generate Java/TypeScript code

```yaml
name: Codegen

on: [push]

jobs:
  codegen:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Setup dpm
        uses: 0xsend/setup-dpm@v1

      - name: Build DAML
        run: dpm build

      - name: Generate Java code
        run: dpm codegen-java

      - name: Generate TypeScript code
        run: dpm codegen-js
```

## What is dpm?

`dpm` is the Digital Asset Package Manager, a drop-in replacement for the deprecated `daml` assistant. It provides commands for:

- **Building DAML projects**: `dpm build`
- **Running tests**: `dpm test`
- **Code generation**: `dpm codegen-java`, `dpm codegen-js`
- **Running sandbox**: `dpm sandbox`
- **And more**: `dpm studio`, `dpm new`, `dpm docs`, etc.

For full documentation, see the [Digital Asset docs](https://docs.digitalasset.com/build/3.4/dpm/dpm.html).

## License

Apache 2.0
