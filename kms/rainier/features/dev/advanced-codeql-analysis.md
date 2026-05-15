# Advanced CodeQL Analysis

- **Category:** dev
- **Status:** active
- **Owners:** security / devex

## Summary

GitHub Actions workflow and shared CodeQL config that move Rainier toward advanced CodeQL setup, initially scanning non-C# languages while C# runtime and matrix design are evaluated separately.

## Behavior

- Runs a GitHub Actions CodeQL matrix for `javascript-typescript`, `python`, `actions`, and `c-cpp` with `build-mode: none`.
- Triggers on pushes to `main`, pull requests to `main`, and a weekly scheduled run.
- Uses least-privilege job permissions and concurrency to cancel superseded runs for the same ref.
- Shared config excludes generated outputs, dependency folders, Python virtual environments/caches, JS/TS bundles, coverage output, Storybook/Vite output, e2e folders, and inactive legacy C/C++ runtime trees.
- C# analysis is intentionally excluded from the active workflow until component timing determines the right matrix split, runner size, and manual build commands.

## Entry Points

- `.github/workflows/codeql.yml` — advanced CodeQL workflow.
- `.github/codeql/codeql-config.yml` — shared exclusion config.

## Key Dependencies

- GitHub Advanced Security / CodeQL code scanning.
- Follow-up timing work for C# analysis before enabling a C# leg.

## Related Features

- [CI Pipelines (Azure DevOps)](ci-pipelines.md)
- [GitHub Release Automation](github-release-automation.md)

## Change Log

- 2026-05-15: PR #9826 feat(dev): create codeql configuration yaml — introduced advanced CodeQL workflow/config including an initial C# matrix leg for timing work.
- 2026-05-15: PR #9827 Create CodeQL Workflow — finalized the workflow around non-C# `build-mode: none` legs and documented C# deferral.
