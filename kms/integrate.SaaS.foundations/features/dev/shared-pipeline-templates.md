# Shared Pipeline Templates

- **Category:** dev
- **Status:** active
- **Owners:** @milliman-lts/platform-engineering

## Summary

Two YAML templates under `.pipelines/templates/` centralise **versioning** and the standard **container-build lifecycle** so every per-service pipeline stays DRY.

## Behavior

### `common-variables.yaml` (variables template)

- `mainBranch = refs/heads/main`.
- `majorVersion = 1`, `minorVersion = 0`.
- `revisionVersion = counter('1.0', 0)` → `BuildNumber = 1.0.$(counter)`.
- `cleanedBranchName` replaces `_` with `-`.
- `VersionSuffix` empty on `main`, else `-<cleanedBranchName>`.
- `pushImages` = true on `main` or when `parameters.forcePush == 'true'`.

### `build-docker-image.yaml` (steps template)

Parameters: `makePrefix`, `pushImages`. Steps:

1. `AzureCLI@2` — `az acr login -n integratesolutions` (subscription "Terraform CI - Dev").
2. `make ${makePrefix}__lint` and `make ${makePrefix}__build` (env: `DEVOPS_PAT=$(System.AccessToken)`, `BUILD_VERSION=$(Build.BuildNumber)`, `TAG=$(Build.BuildNumber)`).
3. `make ${makePrefix}__scan_image` (Trivy).
4. `make ${makePrefix}__publish` (conditional on `pushImages`).

## Entry Points

- `.pipelines/templates/common-variables.yaml` (23 lines).
- `.pipelines/templates/build-docker-image.yaml` (36 lines).
- Consumers: every `*/.pipelines/*.build.yaml` file.

## Key Dependencies

- Azure DevOps YAML templates.
- `AzureCLI@2` task + ACR.

## Related Features

- [Azure DevOps Pipelines](./azure-devops-pipelines.md)
- [Three Musketeers Make](./three-musketeers-make.md)

## Change Log

- 2026-04-21: Seeded.
