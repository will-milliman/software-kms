# Main → SDK Branch Sync

- **Category:** dev
- **Status:** active
- **Owners:** devex / Calc Engine team

## Summary

A GitHub Action that continuously mirrors the `main` branch into the most recent `release/sdk/v*` branch so that Calc Engine SDK consumers always track upstream changes until the SDK release is cut.

## Behavior

- Triggered on pushes to `main`.
- Resolves the highest-versioned `release/sdk/v*` branch.
- Performs a merge (or fast-forward) from `main` into that branch and pushes.
- Conflicts cause the workflow to fail and require manual resolution.

## Entry Points

- `.github/workflows/main-to-sdk-sync.yml` (name approximate).

## Key Dependencies

- [SDK AI Agent](sdk-ai-agent.md) contextual documentation.

## Related Features

- [CI Pipelines (Azure DevOps)](ci-pipelines.md)
- [Calc Engine Bundles](calc-engine-bundles.md)

## Change Log

- 2026-04-21: Seeded.
- 2026-05-07: PR #9633 chore: sync main into release/sdk/v0.4.0 — automated sync carried main into the SDK v0.4 release branch.
- 2026-05-07: PR #9636 chore: sync main into release/sdk/v0.4.0 — automated SDK v0.4 branch sync.
- 2026-05-07: PR #9647 chore: sync main into release/sdk/v0.4.0 — automated SDK v0.4 branch sync.
- 2026-05-07: PR #9656 chore: sync main into release/sdk/v0.4.0 — automated SDK v0.4 branch sync.
- 2026-05-07: PR #9684 chore: sync main into release/sdk/v0.5.0 — automated sync started for the SDK v0.5 release branch.
- 2026-05-07: PR #9695 chore: sync main into release/sdk/v0.5.0 — automated SDK v0.5 branch sync.
- 2026-05-07: PR #9709 chore: sync main into release/sdk/v0.5.0 — automated SDK v0.5 branch sync.
