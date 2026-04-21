# Dependabot

- **Category:** dev
- **Status:** active
- **Owners:** UI Platform Team

## Summary

GitHub Dependabot is configured for weekly npm updates across 12 workspace directories.

## Behavior

- Single npm ecosystem entry in `.github/dependabot.yml` (`:3`), `schedule.interval: weekly`.
- Scanned directories (12):
  - repo root
  - `packages/auth`, `packages/form`, `packages/formatter`, `packages/frame`, `packages/grid`, `packages/modal`, `packages/store`, `packages/themes`
  - `apps/web/sandbox-01`, `apps/web/sandbox-idm-adapter`, `apps/web/sandbox-tenant-manager`
  - `tools/storybook-host`
- **Not scanned:** `packages/cookie-consent`, `packages/federation`, `packages/idm-adapter` (the last uses its own `.npmrc` + npm).
- Note: the `packages/formatter` directory does not currently exist (ghost package) — Dependabot may log a benign error there.

## Entry Points

- `.github/dependabot.yml:1` — config file.

## Key Dependencies

- GitHub Dependabot service.

## Related Features

- [Husky + lint-staged](./husky-lintstaged.md) — gates merges after Dependabot PRs.
- [ADO Validate Pipeline](./ado-validate-pipeline.md) — validates Dependabot PRs.

## Change Log

- 2026-04-21: Seeded.
