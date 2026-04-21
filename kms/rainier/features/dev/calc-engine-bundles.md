# Calc Engine Bundles

- **Category:** dev
- **Status:** active
- **Owners:** mg-alfa / calc engine

## Summary

The MG-ALFA Calc Engine (APL workspace + native support DLLs) is packaged into versioned NuGet bundles so each rainier release pins an exact calc-engine build and end-users can upgrade deliberately ([Calc Engine Upgrade](../business/governance.md)).

## Behavior

- Bundle pipeline collects the built APL workspace, native DLLs, and metadata, and publishes a NuGet with a semver + build suffix.
- Consumers (Integrate installer, Projection Execution) reference specific bundle versions via `PackageReference` or runtime config.
- Older bundles are retained so existing projects can reproduce prior runs exactly.

## Entry Points

- `monorepo/mg-alfa/.pipelines/` calc-engine pack jobs.

## Key Dependencies

- [APL Interface / Transactions](apl-interface.md).
- [Versioning Pipelines](versioning-pipelines.md).

## Related Features

- [Governance](../business/governance.md) (calc-engine upgrade)
- [Projection Execution Regression Suite](projection-regression-suite.md)

## Change Log

- 2026-04-21: Seeded.
