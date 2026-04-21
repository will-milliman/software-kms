# ADO Validate Pipeline

- **Category:** dev
- **Status:** active
- **Owners:** UI Platform Team

## Summary

Azure DevOps pipeline `pipelines/validate.yml` triggered on the `main` branch. Installs dependencies, builds the monorepo, lints, typechecks, runs unit tests, and runs the Tenant Manager Playwright E2E suite. The legacy `idm-adapter` is validated in a separate job using `npm`.

## Behavior

- Trigger: `main` branch only (`validate.yml:1-2`).
- Single stage `Validate_IntegrateCoreUiNext` with three jobs:
  - **`FE_Validate`** (ubuntu-latest, `:12-44`) — corepack pnpm@9.7.1 → `pnpm install` (`Cache@2` keyed on `pnpm-lock.yaml`) → `pnpm run build` → `pnpm run lint && pnpm run typecheck && pnpm run test`.
  - **`E2E_Validate`** (windows-latest, `:46-90`) — install + build → `pnpm exec playwright install` → `pnpm --filter sandbox-tenant-manager run e2e`. Publishes the `E2E_Playwright_Report` artifact from `apps/web/sandbox-tenant-manager/e2e/test-results` on both pass and fail via `PublishBuildArtifacts@1`.
  - **`IdmAdapter_Validate`** (ubuntu-latest, `:92-112`) — separate authenticated `npm install` + `build` for `packages/idm-adapter` (uses its own `.npmrc` outside the pnpm workspace).
- Uses `npmAuthenticate@0` for feed credentials.

## Entry Points

- `pipelines/validate.yml:1` — whole pipeline.
- `pipelines/validate.yml:12-44` — `FE_Validate`.
- `pipelines/validate.yml:46-90` — `E2E_Validate` + artifact publish.
- `pipelines/validate.yml:92-112` — `IdmAdapter_Validate`.

## Key Dependencies

- Azure DevOps built-in tasks: `npmAuthenticate@0`, `Cache@2`, `PublishBuildArtifacts@1`.
- Playwright 1.52.0 (root devDep).
- pnpm 9.7.1, Node 22.

## Related Features

- [ADO Deploy Pipeline](./ado-deploy-pipeline.md) — triggered on this pipeline's successful completion.
- [Legacy IDM Adapter SPA (business)](../business/idm-adapter-legacy-spa.md) — target of `IdmAdapter_Validate`.

## Change Log

- 2026-04-21: Seeded.
