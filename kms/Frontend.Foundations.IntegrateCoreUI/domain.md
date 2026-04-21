# Domain

## Glossary

- **Integrate** — Milliman's actuarial / risk platform. The product ecosystem these UIs belong to.
- **ICUN (Integrate Core UI Next)** — the new React-based shared UI framework in this repo; successor to the legacy AngularJS 1.5 Integrate Core UI. Also referred to as "ICU" in code (package scope `@integrate-core-ui` abbreviated as `Icu*` in component aliases, e.g., `IcuContextProvider`, `IcuEmitterProvider`, `IcuModalProvider`).
- **One Integrate** — umbrella product name used in deployed asset paths (`OneIntegrate/remotes/<name>/`) and as the default tracing prefix in the federation emitter.
- **Shell / Frame** — the outer application chrome: Header, SideNav, Toolbox, TabbedResources, WindowControls. Provided by `@integrate-core-ui/frame`.
- **Toolbox** — a collapsible side panel that hosts contextual "tools" related to the currently active resource. Backed by `useToolbox()` from `@integrate-core-ui/store`.
- **Tool** — an individual panel inside the Toolbox; added via `addTool(...)`; represented as a `TabItem` with `pinned`, `active`, `visible`, `containerIndex` flags.
- **Tabbed Resources** — a top-level tabbed area showing open "resources" (documents, projects, grids, etc.). Backed by `useTabbedResources()`.
- **Resource** — an open tab in Tabbed Resources; a `TabItem`. Example: a Project, a Job History view, a Theme Builder pane.
- **Explorer** — a third tab-based surface (`useExplorer()`) provided alongside Tabbed Resources and Toolbox.
- **TabItem** — the core entity in the tab stores: `{ id, title, pinned, active, visible, containerIndex, ... }`. CRUD provided by the `createTabContext` factory.
- **Resource Context / Tool Context** — per-tab React contexts (`ResourceContext`, `ToolContext`) that expose the currently active resource/tool to descendants via `useResource()` / `useTool()`.
- **Integrate Data Manager (IDM)** — Milliman's data management subsystem. In this repo, "IDM" refers to the legacy AngularJS 1.5 SPA shipped in `@integrate-core-ui/idm-adapter` and hosted for local dev by `sandbox-idm-adapter`. It is **not** the same as generic "identity management" — though the adapter itself uses `oidc-client` for its own auth.
- **Tenant Manager** — the admin UI (sandbox) for managing tenants, environments, groups, roles, permissions, and assignments. The canonical IDM-admin SPA demoed by `sandbox-tenant-manager`.
- **Tenant** — a customer organization identified by a `domain` and `name` (`apps/web/sandbox-tenant-manager/src/store/models/tenant.ts:45`). A Tenant has multiple Environments, Groups, and Roles.
- **Environment** — a deployment/namespace within a Tenant: `{ friendlyName, name }` (`tenant.ts:17`). Groups and roles are scoped to environments.
- **Group** (Tenant Group) — `{ id, isAdmin, name }` (`tenant.ts:22`). Groups receive role assignments.
- **Role** — a named set of permissions within a Tenant. Roles can have `opsOnly` permissions that are hidden from non-ops admins.
- **Permission** — `{ id, key, section, name, description, tier, opsOnly }` (`tenant.ts:35`). Grouped by `section`; filtered by tier and by `opsOnly` based on caller (`endpoints/tenants.ts:27`).
- **Tier** — a Tenant-wide licensing/feature-gating level; `TenantDetail` carries a `tier` which filters visible permissions.
- **Ops Admin** vs **Tenant Admin** — caller roles. `tenantsApi.useAdminCheckQuery` returns `{ isOpsAdmin, isTenantAdmin }` from `/api/services/tenant-manager/tenantmanager/admincheck`; ops admins see `opsOnly` permissions.
- **Assignment** — the mapping of Groups to Roles within an Environment. Managed on the `/assignment` route via `addGroupRole` / `deleteGroupRole` mutations.
- **Gateway** — the primary API backend (`https://milliman.gateway` in dev). RTK Query uses the reducer path `gateway` (`tenant.api.ts:12`).
- **BaseUrlKeys** — enumeration of named backend base URLs used by the tenant-manager RTK Query dynamic base-query: `PROJECT_MANAGER`, `RUN_MANAGER`, `GOVERNANCE`, `GATEWAY`, `IDM`, `MMD`, `ACL` (`base-url.slice.ts:6-14`).
- **Return URL** — the URL the user was navigating to before auth redirect. Persisted via `@integrate-core-ui/auth`'s `saveReturnUrl()` / `getReturnUrl()`.
- **Silent Auth** — OIDC silent token renewal performed in a hidden iframe. Built as a standalone UMD bundle (`silentAuth.js`) by `packages/auth/vite.silent-auth.config.ts`.
- **Feature flag** (auth rollout) — the new `@integrate-core-ui/auth` module was rolled out behind a feature flag per ADR 2025-03-01; the legacy AngularJS auth remains as a fallback.
- **MFE (Micro-Frontend)** — planned composition unit (see ADR 2026-02-16). Each MFE owns a URL prefix and a minimal contract: React component + route + pub/sub event subscription.
- **Host / Remote** — MFE terminology. The Host owns the shell and routing; Remotes are feature modules. Currently only aspirational in this repo (no runtime MF).
- **Integration Gate** — ADO pipeline template (`pipelines/templates/integration-gate.yml@host`) that boots the Host and runs its Playwright smoke tests against a locally-served remote build. Planned (not implemented).
- **ModalService** — the `@integrate-core-ui/modal` extension of `@ebay/nice-modal-react`. Modals declared via `ModalService.create(...)` gain an `ErrorContext` exposing `{ error, setError }` from `ModalService.useModal()`.
- **GlobalThemeProvider** vs **InlineThemeProvider** — full-page vs inline theming scopes. `GlobalThemeProvider` writes `document.documentElement.dataset.carbonTheme` and persists the active theme in `localStorage['milliman-theme']`. `InlineThemeProvider` wraps a subtree in the alternate theme without touching the root.
- **MillimanThemes** — enum of available Carbon themes: `milliman-white`, `milliman-g100` (`themes/src/milliman/inline-theme.provider.tsx:5`).
- **AG Grid Quartz themes** — Milliman-branded Quartz variants (`white`, `whiteTree`, `g100`, `g100Tree`) built with `themeQuartz.withParams({...})` pulling values from Carbon CSS custom properties (`--cds-*`) so theme toggles propagate automatically (`packages/themes/src/milliman/ag-grid.ts`).
- **Verdaccio local registry** — local npm proxy at `http://localhost:4873/` for dry-running publishes before pushing to the ADO `rainier-latest` feed.
- **Rainier** — the ADO npm feed (`rainier-latest`) where `@integrate-core-ui/*` packages are published. Referenced by the `publish:feed` script and `pipelines/deploy.yml:75-76`.
- **Sandbox (01 / IDM Adapter / Tenant Manager)** — the three demo applications under `apps/web/*`. Not production artifacts; serve as integration tests, showcases, and development sandboxes. Deployed statically to `ltssharedassets` for review.
- **Theme Builder** — a feature inside `sandbox-01` that lets a developer edit Carbon theme tokens interactively and export the result in multiple formats (`apps/web/sandbox-01/src/theme-builder/`).

## Core entities & relationships

### Tab-store domain (`@integrate-core-ui/store`)

```
Application
  ├── TabbedResources (one instance)
  │     └── TabItem[]  (resources)  ── ResourceContext ──► active Resource
  ├── Toolbox (one instance)
  │     └── TabItem[]  (tools)      ── ToolContext     ──► active Tool
  └── Explorer (one instance)
        └── TabItem[]
```

A `TabItem` has CRUD operations produced by `createTabContext(contextId)`:

- `addTab / addResource / addTool` — push a new tab; optionally pinned, active, container-indexed.
- `removeTab / removeResource / removeTool` — remove by id.
- `updateTab` — merge fields.
- `activateTab` — set active; de-activates siblings.
- `pinTab / unpinTab`.
- `setVisible` / `setContainerIndex` — drive multi-column toolbox layouts.

### Tenant Manager domain (`apps/web/sandbox-tenant-manager`)

```
Tenant  (domain, name, tier)
  ├── Environment (friendlyName, name)
  │     └── EnvironmentDetails
  │           ├── customPermissions
  │           └── groupMap
  ├── Group (id, isAdmin, name)
  └── Role
        └── Permission (id, key, section, name, description, tier, opsOnly)

Assignment:
  Group ──assigned to──► Role   (within an Environment)
  Role  ──includes────► Permission[]
```

Active-selection state in Redux `tenantSlice` (`src/store/slices/tenant/tenant.slice.ts`):

- `activeTenant`, `activeEnvironment`, `activeGroup`, `activeRole`.
- Auto-seeded from the first API response (`tenant.slice.ts:19-33`).

### Auth domain (`@integrate-core-ui/auth`)

```
AuthManager (singleton)
  ├── SecurityConfig       — authority, client_id, scopes, timeouts, flags
  ├── AuthLogger           — prefixed [Auth Manager] logger
  ├── TokenRenewalManager  — periodic silent renewal
  └── User (oidc-client)   — access_token, id_token, expires_at, profile

ErrorType (enum, AuthManager.ts:30) — standardized failure categories
ReturnUrl utilities — persist + retrieve across redirects
```

Isolation invariant from ADR 2025-03-01: `AuthManager` runs only in a hidden Electron renderer. The token never exists in the main renderer's `localStorage`.

### Federation domain (`@integrate-core-ui/federation`)

```
Host
  └── createEmitter({ tracePrefix })  → Emitter (frozen, tseep-backed)
       └── IcuEmitterProvider value={emitter}
             └── Remote (React subtree)
                   └── useEmitter()   — throws if no provider
```

`MfeProps` (exported type) defines the minimal contract a Remote component receives. `CreateEmitterOptions` controls `tracePrefix` (defaults to `[One Integrate]`).

## Business rules encoded in code

### Authentication

- Node.js 22.x is mandated; Node 24 breaks MSW tests due to an upstream `AbortSignal` bug (`README.md:96`).
- Auth tokens are stored only in the hidden auth window's `localStorage` — never the main renderer's (architectural invariant from ADR 2025-03-01).
- `AuthManager` is a strict singleton (`AuthManager._instance`). Attempting to instantiate twice returns the existing instance.
- On every silent renewal tick, `TokenRenewalManager` asks `AuthManager` for a new token before the current one expires.

### Tenant Manager

- Non-ops admins must not see `opsOnly` permissions. The filter is applied in `src/store/endpoints/tenants.ts:27` when composing `TenantDetail`.
- Permissions are further filtered by the tenant's `tier` — licensing-gated capabilities are hidden.
- `/` route always redirects to `/groups` (`src/routes/index.tsx:3`) — groups is the default landing page.
- All API calls require a bearer token in the `Authorization` header, injected by `dynamicBaseQuery` (`base-url.slice.ts:36-55`). Requests made before `setAuthToken` is dispatched will fail.
- Sign-out is currently a confirmation-only modal (`src/components/sign-out/sign-out.modal.tsx:13`) — it does not clear any session. This is a sandbox-level mock; real sign-out integration is out of scope here.
- The Gateway base URL is pinned to `https://milliman.gateway` by default (`src/store/store.ts:20`); override via `setBaseUrl` Redux action.
- When the active tenant/environment/group/role is not set, `tenant.slice.ts:19-33` auto-selects the first item returned by the API.

### AG Grid

- A Milliman AG Grid Enterprise license key is baked into `packages/grid/src/ag-grid/ag-grid.tsx:22`. Consuming apps must not ship their own.
- AG Grid modules must be registered via the `modules` array re-exported from `@integrate-core-ui/grid/ag-grid` (clipboard, column-tool-panel, menu, range-selection, row-grouping, set-filter, side-bar, client-side-row-model).
- AG Grid theme follows Carbon theme via CSS custom property bridging in `themes/src/milliman/ag-grid.ts` — toggling the Carbon theme re-themes the grid automatically.

### Theming

- Active Milliman theme is persisted in `localStorage['milliman-theme']`.
- If no value is persisted, `usePrefersDarkScheme()` picks between `milliman-white` and `milliman-g100` based on `prefers-color-scheme`.
- `GlobalThemeProvider` sets `document.documentElement.dataset.carbonTheme` so Carbon's CSS variables apply globally.
- `InlineThemeProvider` scopes the alternate theme to a subtree without touching the root dataset — used for mixed-theme compositions (e.g., a light panel inside a dark shell).

### Cookie consent

- On "Accept", a cookie named `cookie_consent` is set with a 1-year expiry.
- The UMD build auto-initializes on `DOMContentLoaded` — it requires no host-page JavaScript (`packages/cookie-consent/src/cookie-consent.ts`, UMD footer `vite.config.mts:54`).
- The UMD URL is case-sensitive — `cookieConsent.js`, not `cookieconsent.js` (deployed to `legalassets/$web`).

### Publishing

- `tools/publish-feed.ts:34-48` treats "version already exists on feed" errors as idempotent success (multiple publish patterns: `"the feed already contains file"`, `"cannot publish over existing version"`, `EPUBLISHCONFLICT`, `E403 + "already exists"`).
- `deploy.yml` runs **only** when triggered by the `IntegrateCoreUiNext` validate pipeline on `refs/heads/main`; all stages guard on `Build.Reason == ResourceTrigger` + source branch.
- `@integrate-core-ui/idm-adapter` is published through a separate path (`tools/publish-feed.ts` on `dist/packages/idm-adapter`) because it lives outside the pnpm workspace.

### Workspace & tooling

- `packages/idm-adapter` is the single workspace-rule exception — excluded from `pnpm-workspace.yaml`, ignored by ESLint (`eslint.config.cjs:16`), and uses its own `npm` + private `.npmrc`.
- `@integrate-core-ui/formatter` is referenced in the build script and path aliases but does not exist on disk — either pending reintroduction or pending cleanup.
- pre-commit runs `eslint` + a **repo-wide** `pnpm -r run typecheck` on staged TS files (`lint-staged.config.cjs`), which can be slow but enforces cross-package type consistency.
