# Shared Pipeline Templates

- **Category:** dev
- **Status:** active
- **Owners:** devex / build engineering

## Summary

Reusable Azure DevOps YAML job templates (`dotnet-build.yml`, `docker-build.yml`, and peers) that standardize how .NET projects are built, tested, packed, and published, and how container images are built and scanned.

## Behavior

- Consumers `extends`/`template:`-reference these files instead of duplicating steps.
- `dotnet-build.yml` handles restore with private feed auth, build, test with coverage, pack, and NuGet push.
- `docker-build.yml` handles build, tag, [Trivy + Checkov Image Scanning](image-scanning.md), and push.
- Parameters expose SKU-specific variation (configuration, feed target, sign, etc.).

## Entry Points

- `.pipelines/templates/` in the repo root (and mirrored per sub-project where needed).

## Key Dependencies

- [CI Pipelines (Azure DevOps)](ci-pipelines.md) consumers.
- [NuGet & npm Feed Setup](package-feed-setup.md) for auth.

## Related Features

- [Trivy + Checkov Image Scanning](image-scanning.md)
- [Versioning Pipelines](versioning-pipelines.md)

## Change Log

- 2026-04-21: Seeded.
