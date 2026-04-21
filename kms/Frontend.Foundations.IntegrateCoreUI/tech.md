# Tech Stack

## Languages & runtimes

- **TypeScript 5.8.3** — primary language across packages and sandbox apps.
- **JavaScript / HTML / Less / SCSS / CSS** — secondary (legacy `idm-adapter` and cookie-consent UMD).
- **Node.js 22.x LTS** — required. Node 24 is explicitly unsupported due to an MSW / `@mswjs/interceptors` `AbortSignal` incompatibility (`README.md:96`).
- **pnpm 9.7.1** — enforced via `packageManager` field in `package.json:18`. No other package managers allowed. `idm-adapter` is the sole exception and uses `npm` + a private `.npmrc`.

## Frameworks

- **React 19.1.0** + **react-dom 19.1.0** with **React Compiler** (`babel-plugin-react-compiler@1.0.0`) enabled in sandbox-01 (`apps/web/sandbox-01/vite.config.mts:21`).
- **Carbon Design System** — `@carbon/react@1.101.0`, `@carbon/styles@1.77.0`, `@carbon/themes@11.50.0`, `@carbon/icons-react@11.56.0`. Foundation for all UI in ICUN.
- **styled-components 6.1.18** — via `babel-plugin-styled-components@2.1.4` in frame, form, and sandbox apps.
- **AG Grid Enterprise 32.3.9** — `@ag-grid-community/*` + `@ag-grid-enterprise/*` modules; license key embedded in `packages/grid/src/ag-grid/ag-grid.tsx:22`.
- **TanStack Router 1.160.0** (file-based) — used by `sandbox-tenant-manager` (hash history). `@tanstack/router-plugin` integrates with Vite.
- **Redux Toolkit 2.8.2** + **react-redux 9.2.0** + **RTK Query** — state management in `sandbox-tenant-manager` (`src/store/tenant.api.ts`). Declared as a peer dep in `@integrate-core-ui/store` but not currently used in that package's source.
- **@ebay/nice-modal-react 1.2.13** — modal service foundation.
- **oidc-client 1.5.4** — OAuth 2.0 / OIDC Implicit Flow in `@integrate-core-ui/auth`.
- **powerbi-client-react 2.0.0** — Power BI report embedding in sandbox-tenant-manager.
- **react-resizable-panels 2.1.7** — splittable toolbox layout.
- **Express 5.1.0** — used only by `sandbox-idm-adapter` as a static + stub-API host.

## Build tooling

- **Vite 6.3.5** — build tool for every live package and sandbox app.
  - `vite-plugin-dts@4.5.4` — emits `.d.ts` types from each library.
  - `vite-plugin-static-copy@3.2.0` — copies `package.json` into each `dist/packages/<name>`.
  - `vite-tsconfig-paths@5.1.4` — respects the repo's TS path aliases.
  - `vite-plugin-css-injected-by-js@3.5.2` — used by some libraries.
  - `vite-plugin-node-polyfills@0.23.0` — polyfills for browser builds where needed.
  - `@vitejs/plugin-react@4.5.1` with Babel plugins: `react-compiler`, `styled-components`.
- **SCSS** compiled with `api: 'modern-compiler'` (`sass@1.89.1`) in packages that ship styles (`frame`, `themes`).
- **Output conventions:** `dist/packages/<name>/index.mjs` + `index.cjs` + `index.d.ts` (plus UMD for `cookie-consent` and `auth/silent-auth`). `cookie-consent` is the only package with `type: "module"`.

## Testing

- **Vitest 3.2.2** with `jsdom 26.1.0`, `@testing-library/react@16.3.0`, `@testing-library/jest-dom@6.6.3`. Setup is a per-package `vitest.setup.ts` that mocks `matchMedia` and registers jest-dom matchers.
- **MSW 2.12.10** — API mocking in `sandbox-tenant-manager` (both browser-side via `setupWorker` and tests via `setupServer`). Worker directory declared at root `package.json:115`.
- **Playwright 1.52.0** — end-to-end tests for `sandbox-tenant-manager` only (`apps/web/sandbox-tenant-manager/e2e/`). Root-level `@playwright/test` dev dep.
- **Coverage:** `@vitest/coverage-v8` with **90% thresholds** enforced in sandbox-tenant-manager (`apps/web/sandbox-tenant-manager/vite.config.mts:59`).

## Lint, format, and hooks

- **ESLint 9.28.0** flat config (`eslint.config.cjs`) combining `@eslint/js`, `typescript-eslint@8.33.1`, `eslint-plugin-react@7.37.5`, `eslint-plugin-react-hooks@5.2.0`, `eslint-plugin-jsx-a11y@6.10.1`, `eslint-plugin-import@2.31.0`, `eslint-plugin-playwright@^1.6.2`. Explicitly ignores `packages/idm-adapter` and `.verdaccio/`.
- **Prettier 3.5.3** with `@trivago/prettier-plugin-sort-imports` for opinionated import ordering (`.prettierrc.cjs`).
- **Husky 9.1.7** pre-commit hook → `pnpm lint-staged --relative` (`.husky/pre-commit`).
- **lint-staged 16.1.0** — runs `eslint` + `pnpm -r run typecheck` on TS files and `prettier --write` on everything else (`lint-staged.config.cjs`).

## Infrastructure & external services

- **ADO npm feed `rainier-latest`** — publication target for `@integrate-core-ui/*` packages (`pipelines/deploy.yml:75-76`).
- **Azure Blob Storage** deploy targets:
  - `ltssharedassets` / `$web` / `IntegrateCoreUiNext/storybook/` → Storybook host.
  - `ltssharedassets` / `$web` / `IntegrateCoreUiNext/web/<site-id>/` → sandbox apps (e.g., `sandbox-01`, `tenant-manager`).
  - `legalassets` / `$web` → `cookie-consent` UMD bundle.
- **Verdaccio 6.1.2** — local npm proxy/registry at `http://localhost:4873/` for publish-flow dry runs (`.verdaccio/`).
- **Milliman Gateway** (`https://milliman.gateway`) — bearer-token API backend consumed by `sandbox-tenant-manager` RTK Query (`apps/web/sandbox-tenant-manager/src/store/store.ts:20`). Tenant-manager API prefix: `/api/services/tenant-manager/`.

## CI / CD

- **Azure DevOps Pipelines** (not GitHub Actions) under `pipelines/`:
  - `validate.yml` — triggered on `main`. Three jobs: `FE_Validate` (install, build, lint, typecheck, test on ubuntu), `E2E_Validate` (Playwright E2E on windows, publishes `E2E_Playwright_Report` artifact), `IdmAdapter_Validate` (separate npm build for the legacy adapter on ubuntu).
  - `deploy.yml` — pipeline-resource-triggered on successful `validate` against `main`. Three stages: `Publish_IntegrateCoreUiNext` (publish packages to `rainier-latest`), `Deploy_Static_Webpages` (Storybook + sandbox-01 to `ltssharedassets`), `Deploy_CookieConsent` (UMD to `legalassets`). All gated on `Build.Reason == ResourceTrigger` + `refs/heads/main`.
- **Dependabot** — weekly npm updates across 12 workspace directories (`.github/dependabot.yml`). Omits `cookie-consent`, `federation`, and `idm-adapter`.
- **No GitHub Actions workflows** present; `.github/` only holds Dependabot config.

## Key root config files

- `package.json:1` — workspace root, scripts (`build`, `lint`, `typecheck`, `test`, `storybook`, `verdaccio`, `publish:feed`, `publish:verdaccio`).
- `pnpm-workspace.yaml:1` — globs `packages/*`, `apps/web/*`, `tools/*`; excludes `packages/idm-adapter`.
- `tsconfig.base.json:17` — `@integrate-core-ui/*` path aliases to each package's `src/index.ts`.
- `.npmrc:1` — `strict-peer-dependencies=false`, `auto-install-peers=true`, `save-exact=true`, registry `https://registry.npmjs.org/`.
- `.vscode/IntegrateCoreUiNext.code-workspace` — recommended multi-root workspace.
