# Frontend.Foundations.IntegrateCoreUI

- **Repo:** [milliman-lts/Frontend.Foundations.IntegrateCoreUI](https://github.com/milliman-lts/Frontend.Foundations.IntegrateCoreUI)
- **Default branch:** `main`
- **Seeded:** 2026-04-21
- **Seed HEAD:** `6bae04b06b3b54ece656a43593b7de9e332719d5`

## Purpose

Framework for building frontend applications in **Integrate** (Milliman's actuarial/risk platform). It is a pnpm-workspace monorepo that ships shared UI libraries, design-system themes, sandbox demo apps, and Storybook — collectively referred to as **Integrate Core UI "Next"** (ICUN), the successor to the legacy AngularJS 1.x UI.

## Target audience

- **Milliman LTS frontend engineers** building feature modules and micro-frontends that sit inside the Integrate shell (desktop/Electron and web).
- **Platform engineers** maintaining the shared chrome (header, side-nav, toolbox, tabbed resources), theming, auth, and modal infrastructure.
- Internal only; published to the ADO `rainier-latest` npm feed under the `@integrate-core-ui` scope.

## High-level capabilities

- **Shared UI component libraries** under `@integrate-core-ui/*`:
  - `frame` — outer app chrome (Header, SideNav, Toolbox, TabbedResources, WindowControls, HelpMenu, ThemeToggle, ContentRegion, SignOut, VersionCopyright, DynamicIcon).
  - `themes` — Milliman-branded Carbon themes (`milliman-white`, `milliman-g100`) + AG Grid Quartz themes wired to Carbon CSS vars.
  - `grid` — AG Grid Enterprise wrapper with Milliman theming, quick filter, no-rows overlay, and context-exposed APIs.
  - `form` — Carbon-based form widgets (currently `ProgressButton`).
  - `modal` — Carbon `ComposedModal` on top of `@ebay/nice-modal-react`, with an error context.
  - `store` — React-context stores for tabbed resources, toolbox, and explorer (factory pattern `createTabContext`).
  - `auth` — OIDC authentication for Electron apps (silent renewal, return-URL persistence, `[Auth Manager]` logger).
  - `cookie-consent` — framework-free cookie banner (ESM + UMD), deployed to the `legalassets` Azure blob.
  - `federation` — shared types + React context for Module Federation pub/sub between host and remotes.
  - `idm-adapter` — legacy AngularJS 1.5 Integrate Data Manager SPA, preserved for embedding (outside the pnpm workspace).
- **Sandbox apps** (`apps/web/`):
  - `sandbox-01` — frame showcase and Theme Builder.
  - `sandbox-tenant-manager` — full IDM admin SPA (Redux Toolkit + RTK Query + TanStack Router + MSW + Playwright).
  - `sandbox-idm-adapter` — Express host serving the legacy AngularJS IDM bundle with stubbed APIs.
- **Storybook host** (`tools/storybook-host/`) aggregating stories from every workspace package.
- **Verdaccio** local npm registry for publish-flow testing.
- **CI/CD** via Azure DevOps pipelines: `validate.yml` (build, lint, typecheck, unit + E2E) and `deploy.yml` (publish to ADO `rainier-latest` feed and deploy static artifacts to Azure blob storage).
- **Architectural decision records** (`docs/decisions/`) covering authentication, avoiding Next.js, Integrated Windows Auth, multi-window Electron, and the Module Federation / MFE strategy.
