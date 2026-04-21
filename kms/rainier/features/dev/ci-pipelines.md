# CI Pipelines (Azure DevOps)

- **Category:** dev
- **Status:** active
- **Owners:** devex / build engineering

## Summary

Per-service, per-subproject Azure DevOps YAML pipelines build, test, pack, and publish every component in the rainier mono-repo. Pipelines are organized under a top-level `.pipelines/` tree plus per-sub-project `.pipelines/` folders.

## Behavior

- Each buildable unit (Core servers, MG-ALFA, MMD, Projection Execution, Cloud services, Desktop UI, etc.) has its own pipeline YAML with `trigger`/`pr` path filters.
- Build/test/pack/publish stages share logic via [Shared Pipeline Templates](shared-pipeline-templates.md).
- Pipelines publish NuGet packages to internal feeds (`central`, `milliman`, `rainier-latest`) and artifacts to pipeline storage for downstream consumption.
- Coverage is gated by [Code Coverage Threshold](code-coverage-threshold.md).

## Entry Points

- Root: `.pipelines/` and `vsts/azure-pipelines.yml` in the repo.
- Per sub-project: e.g. `monorepo/mg-alfa/.pipelines/`, `monorepo/projection-execution/.pipelines/`, `monorepo/model-development/.pipelines/`, `monorepo/desktop-core/.pipelines/`, `monorepo/risk-neutral-esg/.pipelines/`, `monorepo/cloud/.pipelines/`, `monorepo/grid-utils-integration/.pipelines/`, `monorepo/tools/.pipelines/`.

## Key Dependencies

- Azure DevOps org `mgalfadev`.
- [NuGet & npm Feed Setup](package-feed-setup.md).
- [Shared Pipeline Templates](shared-pipeline-templates.md).

## Related Features

- [Versioning Pipelines](versioning-pipelines.md)
- [Playwright E2E Orchestration](playwright-e2e-orchestration.md)
- [Calc Engine Bundles](calc-engine-bundles.md)

## Change Log

- 2026-04-21: Seeded.
