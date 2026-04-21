# Features

## Business (user-visible capabilities)

| Feature | File | Summary |
| --- | --- | --- |
| App Shell Frame | [business/app-shell-frame.md](business/app-shell-frame.md) | The shared Integrate chrome — Header, SideNav, Toolbox, TabbedResources, WindowControls, HelpMenu. |
| Theme Toggle | [business/theme-toggle.md](business/theme-toggle.md) | User-facing light/dark Milliman theme switching; persisted across sessions. (Dev counterpart: [dev/theme-providers.md](dev/theme-providers.md)) |
| Tabbed Resources | [business/tabbed-resources.md](business/tabbed-resources.md) | Opening, activating, pinning, and closing resource tabs inside the shell. |
| Toolbox | [business/toolbox.md](business/toolbox.md) | Collapsible side panel that exposes contextual tools for the active resource. |
| Authentication (Sign-In) | [business/authentication.md](business/authentication.md) | OIDC sign-in flow for Electron desktop Integrate apps, with silent renewal and return-URL persistence. (Dev counterpart: [dev/auth-library.md](dev/auth-library.md)) |
| Cookie Consent Banner | [business/cookie-consent.md](business/cookie-consent.md) | Framework-free cookie banner deployed to Milliman legal assets. |
| Tenant Management | [business/tenant-management.md](business/tenant-management.md) | Admin UI to manage tenants, environments, groups, roles, permissions, and assignments (sandbox-tenant-manager). |
| Theme Builder | [business/theme-builder.md](business/theme-builder.md) | Interactive Carbon token editor with multi-format export (sandbox-01). |
| Legacy IDM Adapter SPA | [business/idm-adapter-legacy-spa.md](business/idm-adapter-legacy-spa.md) | The preserved AngularJS 1.5 Integrate Data Manager experience, served via sandbox-idm-adapter. |
| Power BI Reports | [business/powerbi-reports.md](business/powerbi-reports.md) | Embedded Power BI report page inside the Tenant Manager. |

## Dev (build, CI, tooling, developer experience)

| Feature | File | Summary |
| --- | --- | --- |
| Auth Library | [dev/auth-library.md](dev/auth-library.md) | `@integrate-core-ui/auth` — singleton `AuthManager`, silent renewal, return-URL utilities. (Business counterpart: [business/authentication.md](business/authentication.md)) |
| Theme Providers | [dev/theme-providers.md](dev/theme-providers.md) | `@integrate-core-ui/themes` — Global/InlineThemeProvider, Milliman Carbon themes, themed AG Grid Quartz. (Business counterpart: [business/theme-toggle.md](business/theme-toggle.md)) |
| Modal Service | [dev/modal-service.md](dev/modal-service.md) | `@integrate-core-ui/modal` — NiceModal-based modal registry with error context. |
| Data Grid Wrapper | [dev/data-grid.md](dev/data-grid.md) | `@integrate-core-ui/grid` — themed AG Grid Enterprise with quick-filter, no-rows overlay, API context. |
| Progress Button | [dev/progress-button.md](dev/progress-button.md) | `@integrate-core-ui/form` — Carbon button + InlineLoading composition. |
| Federation Pub/Sub | [dev/federation-pubsub.md](dev/federation-pubsub.md) | `@integrate-core-ui/federation` — emitter factory and React context for host/remote communication. |
| Store Contexts | [dev/store-contexts.md](dev/store-contexts.md) | `@integrate-core-ui/store` — `createTabContext` factory + tabbed-resources, toolbox, explorer providers. |
| Storybook Host | [dev/storybook-host.md](dev/storybook-host.md) | Aggregates `*.stories.*` from every workspace package; deployed to Azure Blob. |
| Verdaccio Local Registry | [dev/verdaccio-local-registry.md](dev/verdaccio-local-registry.md) | Local npm registry harness for publish-flow testing. |
| ADO Validate Pipeline | [dev/ado-validate-pipeline.md](dev/ado-validate-pipeline.md) | `pipelines/validate.yml` — build, lint, typecheck, unit + E2E. |
| ADO Deploy Pipeline | [dev/ado-deploy-pipeline.md](dev/ado-deploy-pipeline.md) | `pipelines/deploy.yml` — publish to `rainier-latest` feed and Azure Blob. |
| Publish Feed Script | [dev/publish-feed-script.md](dev/publish-feed-script.md) | `tools/publish-feed.ts` — idempotent `npm publish` wrapper. |
| Dependabot | [dev/dependabot.md](dev/dependabot.md) | Weekly npm updates across workspace directories. |
| Husky + lint-staged | [dev/husky-lintstaged.md](dev/husky-lintstaged.md) | Pre-commit ESLint, Prettier, and repo-wide typecheck. |
| Playwright CLI Agent Skill | [dev/playwright-cli-skill.md](dev/playwright-cli-skill.md) | `.agents/skills/playwright-cli/` — agent playbook for browser-driven frontend validation. |
| Sandbox Apps | [dev/sandbox-apps.md](dev/sandbox-apps.md) | `apps/web/*` — three demo hosts exercising the shared libraries. |
| MSW API Mocking | [dev/msw-api-mocking.md](dev/msw-api-mocking.md) | Tenant-manager MSW handlers + worker for tests and in-browser mocks. |
| Workspace & Monorepo Tooling | [dev/workspace-tooling.md](dev/workspace-tooling.md) | pnpm workspaces, tsconfig path aliases, ESLint flat config, Prettier import sorter. |
| Planned MFE Architecture | [dev/mfe-architecture-planned.md](dev/mfe-architecture-planned.md) | Documented (not yet implemented) Rspack Module Federation strategy. |
