# Playwright CLI Agent Skill

- **Category:** dev
- **Status:** active
- **Owners:** UI Platform Team

## Summary

A repo-local agent "skill" under `.agents/skills/playwright-cli/` that teaches an AI assistant how to validate frontend changes by driving a real browser via a `playwright-cli` tool — capturing snapshots, screenshots, console output, network traces, and videos.

## Behavior

- The skill documents an interactive workflow: probe `localhost` first; reuse a running dev server if present, otherwise start one; `playwright-cli open` → `snapshot` to get element refs (`e15`, etc.) → interact via refs → capture artifacts under `tmp/playwright-cli/`.
- Lists the available commands: `snapshot`, `click`, `fill`, `screenshot`, `eval`, `console error`, `network`, `tracing-start/stop`, `video-start/stop`, session flags `-s=<name>`.
- Install fallback: `npm install -g @playwright/cli@latest`.
- The `references/integrate-core-ui-frontends.md` companion lists the three sandbox targets, their ports, and preview URLs:
  - `sandbox-01` — dev `http://localhost:4200`, preview `http://localhost:4300/IntegrateCoreUiNext/web/sandbox-01`.
  - `sandbox-tenant-manager` — dev `:4201`, preview `:4301/IntegrateCoreUiNext/web/tenant-manager/`, E2E `pnpm --filter sandbox-tenant-manager run e2e`.
  - `sandbox-idm-adapter` — Express host at `:3001`.

## Entry Points

- `.agents/skills/playwright-cli/SKILL.md:1` — main playbook (214 lines).
- `.agents/skills/playwright-cli/SKILL.md:150` — install fallback.
- `.agents/skills/playwright-cli/references/integrate-core-ui-frontends.md:1` — per-app targets.

## Key Dependencies

- `@playwright/cli` / `@playwright/test`.

## Related Features

- [Sandbox Apps](./sandbox-apps.md)
- [ADO Validate Pipeline](./ado-validate-pipeline.md) — uses Playwright test runner.

## Change Log

- 2026-04-21: Seeded.
