# NuGet & npm Feed Setup

- **Category:** dev
- **Status:** active
- **Owners:** devex

## Summary

Authentication and configuration for Azure DevOps-hosted NuGet and npm package feeds (`central`, `milliman`, `rainier-latest`) used by both developer machines and CI.

## Behavior

- `NuGet.Config` at repo root + per-subtree points at the three internal feeds and `nuget.org`.
- Developers run `vsts-npm-auth` (Windows) to refresh npm credentials stored in `~/.npmrc`.
- NuGet auth uses a PAT via `NuGetCredentialProvider` or env vars in CI; Azure DevOps service connections in pipelines.
- See repo `README.md` setup section for step-by-step.

## Entry Points

- `NuGet.Config` (multiple locations).
- `monorepo/desktop/ui/.npmrc`, `.pnpmfile.cjs`.
- Root `README.md` setup sections.

## Key Dependencies

- Azure DevOps org `mgalfadev` feeds.

## Related Features

- [CI Pipelines (Azure DevOps)](ci-pipelines.md)
- [Shared Pipeline Templates](shared-pipeline-templates.md)

## Change Log

- 2026-04-21: Seeded.
- 2026-05-07: PR #9693 update monorepo to GU.15.0.103 — updated monorepo GridUtils package version to 15.0.103.
- 2026-05-07: PR #9713 Remove XmlSerializers.dll references dropped in GridUtils 15.0.103 — removed obsolete `XmlSerializers.dll` references after the GridUtils 15.0.103 update.
