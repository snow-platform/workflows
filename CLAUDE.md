# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repository is

`snow-platform/workflows` holds the organization's **reusable GitHub Actions workflows**. There is no
application code, no package manifest, and no build/lint/test tooling — the entire product is the set of
YAML files in `.github/workflows/`. Nothing here runs in this repository; each workflow is consumed by other
repositories at the *job* level:

```yaml
jobs:
  ci:
    uses: snow-platform/workflows/.github/workflows/default-check-dotnet.yml@main
    with:
      version: '10.0.x'
```

Because consumers pin `@main`, any change merged to `main` takes effect immediately across every repository
that references it. Treat edits to existing workflows as breaking changes to downstream CI: renaming or
adding a required `inputs:` entry will fail every caller that does not pass it.

## Conventions used by the existing workflows

- Filenames follow `default-check-<ecosystem>.yml`.
- The top-level `name:` is set to the filename *including* the `.yml` extension (e.g. `name: default-check-node.yml`),
  not a prose title. Match this when adding a workflow.
- Trigger is `on: workflow_call` only — never `workflow_dispatch` or `push`. These are libraries, not
  standalone pipelines (see commit 8f30bc9, which converted the first workflow from dispatch to call).
- Each takes a single required `version` string input, forwarded to the ecosystem's `setup-*` action, so the
  caller controls the toolchain version.
- The single job is named `check` and runs on `ubuntu-latest`.
- Actions are pinned to major tags (`actions/checkout@v6`, `actions/setup-dotnet@v6`, `actions/setup-node@v5`).
- Step names are lowercase (`restore`, `build`, `test`, `install dependencies`).

## Verifying changes

There is no local test harness. Validate by reading the YAML carefully and, when possible, by triggering a
run from a consumer repository against a branch ref (`...@<branch>`) before merging to `main`.

## Adding a workflow

1. Create `.github/workflows/default-check-<ecosystem>.yml` following the conventions above.
2. Add a `### \`<filename>\`` section to README.md with a one-line description and a usage snippet showing
   the `with:` inputs — the README is the only documentation consumers have. (Note: `default-check-node.yml`
   was added without a README entry; adding it is a worthwhile fix.)
3. Commit with the repository's convention: `ci: <lowercase description>`, wrapping filenames in backticks.
