# Default Workflows

The default workflows for the organization.

## Usage

Reference a workflow with `uses:` at the **job** level in any `.github/workflows/*.yml`

```yaml
name: CI

on:
  pull_request:
  push:
    branches: [main]

jobs:
  ci:
    uses: snow-platform/workflows/.github/workflows/[name]@main
```

## Workflows

### `default-check-dotnet.yml`

Restores, builds and tests a .NET solution.

```yaml
jobs:
  ci:
    uses: snow-platform/workflows/.github/workflows/default-check-dotnet.yml@main
    with:
      version: '10.0.x'
```