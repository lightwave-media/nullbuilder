# ⚰️ FOLDED into [lightwave-media/lightwave-ai](https://github.com/lightwave-media/lightwave-ai) — `src/nullbuilder/`

> **This repository is archived and read-only. Do not push.**
>
> Folded with **full git history** into the lightwave-ai monorepo at
> [`src/nullbuilder/`](https://github.com/lightwave-media/lightwave-ai/tree/main/src/nullbuilder) at `dd64006a6`, 2026-08-10
> (epic [lightwave-ai#44](https://github.com/lightwave-media/lightwave-ai/issues/44)).
>
> Open issues were transferred and relabelled `package:nullbuilder`. Tags moved as `nullbuilder/vX.Y.Z`.
> The public `nullclaw/nullbuilder` upstream (where one exists) is unaffected.

---

# nullbuilder

Shared GitHub Actions workflows for NullClaw Zig projects.

## Workflows

Use `@v1` from project repositories.

### CI

```yaml
jobs:
  ci:
    uses: nullclaw/nullbuilder/.github/workflows/zig-ci.yml@v1
    permissions:
      contents: read
    with:
      binary_name: nullclaw
      artifact_prefix: nullclaw
```

Projects with generated assets can install Node, run setup hooks, customize the
matrix, and attach one E2E command to a single target:

```yaml
jobs:
  zig:
    uses: nullclaw/nullbuilder/.github/workflows/zig-ci.yml@v1
    permissions:
      contents: read
    with:
      binary_name: nullhub
      artifact_prefix: nullhub
      node_version: 22
      node_cache_dependency_path: ui/package-lock.json
      test_command: zig build test -Dembed-ui=false -Dbuild-ui=false --summary all
      pre_build_command: |
        npm --prefix ui ci --no-audit --no-fund
        npm --prefix ui run build
      build_args: -Dbuild-ui=false
      e2e_command: bash tests/test_e2e.sh
      targets_json: >-
        [
          {"os":"ubuntu-latest","target":"linux-x86_64","zig_target":"x86_64-linux-musl"},
          {"os":"ubuntu-latest","target":"linux-aarch64","zig_target":"aarch64-linux-musl"},
          {"os":"macos-latest","target":"macos-aarch64","zig_target":"aarch64-macos"},
          {"os":"windows-latest","target":"windows-x86_64","zig_target":"x86_64-windows"}
        ]
```

### Nightly

```yaml
jobs:
  nightly:
    uses: nullclaw/nullbuilder/.github/workflows/zig-nightly.yml@v1
    permissions:
      actions: read
      contents: read
    with:
      binary_name: nullclaw
      artifact_prefix: nullclaw
      force: ${{ inputs.force || false }}
```

### Release

```yaml
jobs:
  release:
    uses: nullclaw/nullbuilder/.github/workflows/zig-release.yml@v1
    permissions:
      contents: write
      packages: write
    secrets: inherit
    with:
      binary_name: nullclaw
      artifact_prefix: nullclaw
      publish_docker: true
```

Release builds support the same Node/pre-build and target-matrix inputs. Projects
that publish generated source archives can enable `source_archive` and provide a
`source_prepare_command`.
