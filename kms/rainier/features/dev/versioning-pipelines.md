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
- `Get-LastTagMatching` (in `.pipelines/versioning/scripts/GitHelpers.ps1`) emits a `Write-Warning` and returns `$null` when `git describe` finds no matching tag for a prefix, instead of throwing. `set-build-version.yaml` falls back to `<tagPrefix>0.0.0` so newly introduced components (e.g. `mmd-desktop-`, `mmd-site-`, `wkhtmltopdf-`, `acl-`, `origin-`) can build before their first base tag is bootstrapped (PR #9614).

## Entry Points

- `.pipelines/versioning*.yml` and supporting PowerShell modules.
- `.pipelines/versioning/scripts/GitHelpers.ps1` — `Get-LastTagMatching` (warn-and-return-null on missing tags).
- `.pipelines/versioning/set-build-version.yaml` — applies `<prefix>0.0.0` fallback when no base tag exists.
- `.pipelines/versioning/tests/GitHelpers.Unit.Tests.ps1` — Pester 5.7.1 unit tests.
- Per-project version files where present.

## Key Dependencies

- [CI Pipelines (Azure DevOps)](ci-pipelines.md).

## Related Features

- [Calc Engine Bundles](calc-engine-bundles.md) — consumes the computed version.
- [GitHub Release Automation](github-release-automation.md).

## Change Log

- 2026-04-21: Seeded.
- 2026-04-22: PR #9614 AB#91257 Versioning: warn instead of throw when no matching tag exists — `Get-LastTagMatching` now warns + returns null on miss, and `set-build-version.yaml` falls back to `<prefix>0.0.0` so first-time component builds succeed before a base tag is bootstrapped.
