# Storybook

- **Category:** dev
- **Status:** active
- **Owners:** desktop-ui

## Summary

A Storybook catalog of Integrate's React component library with stories that double as Playwright visual regression tests, published via an Azure DevOps pipeline.

## Behavior

- Stories live alongside components in the UI monorepo.
- The Storybook build produces a static site that's hosted internally.
- Visual tests capture screenshots per story and diff against committed baselines; regressions fail the build.

## Entry Points

- `monorepo/desktop/ui/apps/storybook/` (or `packages/*/stories/`).
- Storybook pipeline YAML under `monorepo/desktop/ui/.pipelines/`.

## Key Dependencies

- [Playwright E2E Orchestration](playwright-e2e-orchestration.md) (shared Playwright infra).

## Related Features

- [CI Pipelines (Azure DevOps)](ci-pipelines.md)

## Change Log

- 2026-04-21: Seeded.
- 2026-05-07: PR #9696 Add GitHub Actions workflow for Storybook GitHub Pages deployment — Storybook can now be deployed through GitHub Pages automation.
- 2026-05-07: PR #9687 feat: add triggered qualys scan for mmd site — MMD site build flow gained a triggered Qualys scan stage.
