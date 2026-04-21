# Versioning Pipelines

- **Category:** dev
- **Status:** active
- **Owners:** devex / build engineering

## Summary

Pipeline logic and PowerShell helpers that compute build/release version numbers (product version + build counter) used across NuGet packages, assemblies, MSIs, and docker image tags.

## Behavior

- A top-level `versioning.yml` (or similar) pipeline runs at the start of builds to resolve the version based on branch/tag conventions.
- Helpers in `shared/` or `src/scripts/` stamp assemblies and adjust `$(Build.BuildNumber)`.
- Output is consumed by downstream pack/publish stages to keep all artifacts aligned.

## Entry Points

- `.pipelines/versioning*.yml` and supporting PowerShell modules.
- Per-project version files where present.

## Key Dependencies

- [CI Pipelines (Azure DevOps)](ci-pipelines.md).

## Related Features

- [Calc Engine Bundles](calc-engine-bundles.md) — consumes the computed version.
- [GitHub Release Automation](github-release-automation.md).

## Change Log

- 2026-04-21: Seeded.
