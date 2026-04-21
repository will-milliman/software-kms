# Projection Execution Regression Suite

- **Category:** dev
- **Status:** active
- **Owners:** projection-execution / qa

## Summary

A baseline-diff regression runner for projection correctness: runs a curated set of projections against the current calc engine build and compares outputs (row-by-row, within tolerance) against committed baselines.

## Behavior

- Baselines are large output sets committed under `monorepo/projection-execution/` (or referenced via LFS).
- The runner parallelizes projections, diffs outputs, and produces an HTML report highlighting numeric deviations.
- Blocks merges on tolerance breaches.

## Entry Points

- `monorepo/projection-execution/regression/` (approximate).
- Pipeline YAML under `monorepo/projection-execution/.pipelines/`.

## Key Dependencies

- [UDF Code Generation](udf-code-generation.md).
- [Calc Engine Bundles](calc-engine-bundles.md).

## Related Features

- [Run Projections](../business/run-projections.md)

## Change Log

- 2026-04-21: Seeded.
