# Playwright E2E Orchestration

- **Category:** dev
- **Status:** active
- **Owners:** desktop-ui / QA

## Summary

A 14-stage end-to-end test catalog for the Integrate Electron desktop app. Each stage installs/launches the packaged app, runs a slice of the Playwright suite against the real Electron process, and reports results as a pipeline artifact.

## Behavior

- Stages are segmented to keep individual run durations manageable and to isolate failures.
- Each stage uses an "app-installer" step that downloads the build artifact (MSI/zip) and installs it into a clean runner before tests execute.
- Tests drive the UI with Playwright's Electron support, including file-system fixtures for sample projects.

## Entry Points

- `monorepo/desktop/ui/.pipelines/` (E2E pipeline YAML).
- `monorepo/desktop/ui/apps/electron/e2e/` (test specs).
- `monorepo/desktop/ui/apps/electron/playwright.config.*`.

## Key Dependencies

- [CI Pipelines (Azure DevOps)](ci-pipelines.md).
- [Playwright CLI Skill (AI-assisted)](playwright-cli-skill.md) for interactive debugging.

## Related Features

- [Storybook](storybook.md) — Playwright is also used for visual tests.

## Change Log

- 2026-04-21: Seeded.
