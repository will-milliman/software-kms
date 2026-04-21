# Sandbox Apps

- **Category:** dev
- **Status:** active
- **Owners:** UI Platform Team

## Summary

`apps/web/` contains three demo applications that exercise the shared libraries end-to-end. They serve as living examples for engineers, host targets for Playwright validation, and static deployment targets for stakeholder review.

## Behavior

- **`sandbox-01`** — Frame + Theme Builder showcase (React 19 + Vite + styled-components + React Compiler). Single-page, no router. Dev `:4200`, preview `:4300/IntegrateCoreUiNext/web/sandbox-01`. Consumes `cookie-consent`, `frame`, `grid`, `modal`, `store`, `themes`.
- **`sandbox-tenant-manager`** — Full IDM admin SPA (Redux Toolkit + RTK Query + TanStack Router + MSW + Playwright). Dev `:4201`, preview `:4301/IntegrateCoreUiNext/web/tenant-manager/`. Consumes `frame`, `grid`, `modal`, `store`, `themes`.
- **`sandbox-idm-adapter`** — Express static + stub-API host on port 3001 that serves the pre-built legacy AngularJS 1.5 IDM SPA from `dist/packages/idm-adapter`, exposing per-module scripts and stubbing the IDM/project APIs. Not a React app.
- None of the sandboxes are Module Federation hosts or remotes today; they're independent SPAs under a shared `/IntegrateCoreUiNext/web/<name>` base path in production.
- Only `sandbox-tenant-manager` has Vitest + Playwright; sandbox-01 has Vitest unit specs (theme-builder); sandbox-idm-adapter has none.

## Entry Points

- `apps/web/sandbox-01/src/main.tsx:50` — root.
- `apps/web/sandbox-01/src/app.tsx:202` — shell.
- `apps/web/sandbox-01/vite.config.mts:8` — prod `base`.
- `apps/web/sandbox-tenant-manager/src/main.tsx:21` — root.
- `apps/web/sandbox-tenant-manager/src/routes/__root.tsx:8` — layout shell.
- `apps/web/sandbox-tenant-manager/vite.config.mts:10` — prod `base` + `vitest` + MSW browser plugin.
- `apps/web/sandbox-tenant-manager/playwright.config.ts:1` — E2E config.
- `apps/web/sandbox-idm-adapter/server.js:1` — Express host.

## Key Dependencies

- React 19, Vite 6, `@carbon/react`, `@tanstack/react-router`, `@reduxjs/toolkit`, `msw`, `@playwright/test`, `powerbi-client-react`, `express`.

## Related Features

- [App Shell Frame (business)](../business/app-shell-frame.md)
- [Theme Builder (business)](../business/theme-builder.md)
- [Tenant Management (business)](../business/tenant-management.md)
- [Legacy IDM Adapter SPA (business)](../business/idm-adapter-legacy-spa.md)
- [MSW API Mocking](./msw-api-mocking.md)
- [Playwright CLI Agent Skill](./playwright-cli-skill.md)

## Change Log

- 2026-04-21: Seeded.
