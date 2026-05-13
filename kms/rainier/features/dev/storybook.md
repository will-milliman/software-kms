# Storybook

- **Category:** dev
- **Status:** active
- **Owners:** desktop-ui

## Summary

A Storybook catalog of Integrate's React component library with stories that double as Playwright visual regression tests, published via GitHub Pages.

## Behavior

- Stories live alongside components in the UI monorepo.
- The Storybook build produces a static site hosted at `https://sturdy-spoon-2qrpy7z.pages.github.io/`.
- Visual tests capture screenshots per story and diff against committed baselines; regressions fail the build.

## Entry Points

- `monorepo/desktop/ui/tools/storybook/` and `packages/*/stories/`.
- GitHub Pages deployment configured outside the removed Azure DevOps Storybook publish pipeline.

## Key Dependencies

- [Playwright E2E Orchestration](playwright-e2e-orchestration.md) (shared Playwright infra).

## Related Features

- [CI Pipelines (Azure DevOps)](ci-pipelines.md)

## Change Log

- 2026-04-21: Seeded.
- 2026-05-07: PR #9696 Add GitHub Actions workflow for Storybook GitHub Pages deployment — Storybook can now be deployed through GitHub Pages automation.
- 2026-05-07: PR #9687 feat: add triggered qualys scan for mmd site — MMD site build flow gained a triggered Qualys scan stage.
- 2026-05-12: PR #9707 Add selectable New Business Source column to Liability Segments grid — added a warning-only server-validation story and visual baseline for AG Grid Query.
- 2026-05-12: PR #9793 Disable Azure Pipeline storybook deployment and update docs — removed the Azure Blob Storybook publish pipeline and changed documentation to the GitHub Pages Storybook URL.
