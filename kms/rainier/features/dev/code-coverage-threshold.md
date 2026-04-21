# Code Coverage Threshold

- **Category:** dev
- **Status:** active
- **Owners:** devex / qa

## Summary

`azurepipelines-coverage.yml` sets a 70% diff-coverage target so PRs that introduce untested code are flagged by Azure DevOps coverage reports.

## Behavior

- Diff coverage is measured against the target branch.
- Below-threshold PRs receive a failing coverage check (advisory or required depending on branch policy).
- Line and branch coverage are both considered.

## Entry Points

- `azurepipelines-coverage.yml` at repo root.

## Key Dependencies

- [CI Pipelines (Azure DevOps)](ci-pipelines.md) coverage collection.

## Related Features

- [Shared Pipeline Templates](shared-pipeline-templates.md)

## Change Log

- 2026-04-21: Seeded.
