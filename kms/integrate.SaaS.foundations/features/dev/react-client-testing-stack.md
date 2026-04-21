# React Client Testing Stack

- **Category:** dev
- **Status:** active
- **Owners:** @milliman-lts/frontend-platform-engineering

## Summary

The tenant-admin ClientApp enforces a **four-layer** testing strategy:

1. **Vitest 3** unit tests — with enforced 90% coverage thresholds (lines/funcs/branches/statements).
2. **MSW 2.7** for API mocking in unit + component tests.
3. **Vitest integration tests** against the Hono mock API run in a Testcontainers-managed Docker container.
4. **Playwright 1.58** end-to-end tests — also against the Hono mock API, with auth bypass via `page.route()` and localStorage seeding; snapshots pinned to `ubuntu-latest` + `chromium`.

## Behavior

- `pnpm test:ci` — unit (Vitest, CI reporter).
- `pnpm test:integration` — Vitest + Testcontainers spins up Hono mock API image.
- `pnpm e2e` — Playwright against the built app + Hono mock.
- Coverage enforced via thresholds in `vite.config.ts:131-137`.
- CI (`tenantadmin.web.build.yaml`) publishes Playwright HTML report + videos as build artifacts.

## Entry Points

- `tenant-admin/src/ClientApp/vite.config.ts:131-137` — coverage thresholds.
- `tenant-admin/src/ClientApp/package.json:7-28` — test scripts.
- `tenant-admin/src/ClientApp/test/integration/` — integration specs.
- `tenant-admin/src/ClientApp/test/mock-api/` — Hono mock API.
- `tenant-admin/src/ClientApp/e2e/` — Playwright specs.
- `tenant-admin/.pipelines/tenantadmin.web.build.yaml` — CI wiring.

## Key Dependencies

- Vitest 3 + `@vitest/coverage-v8`.
- MSW 2.7.5.
- Playwright 1.58.2.
- Testcontainers 10.24.2 (npm).
- Hono (mock API).

## Related Features

- [Hono Mock API](./hono-mock-api.md)
- [Azure DevOps Pipelines](./azure-devops-pipelines.md)

## Change Log

- 2026-04-21: Seeded.
