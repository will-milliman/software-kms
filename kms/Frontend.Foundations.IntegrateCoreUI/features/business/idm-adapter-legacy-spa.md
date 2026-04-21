# Legacy IDM Adapter SPA

- **Category:** business
- **Status:** active (legacy; preserved for embedding)
- **Owners:** UI Platform Team

## Summary

The preserved AngularJS 1.5 Integrate Data Manager (IDM) SPA. Shipped as `@integrate-core-ui/idm-adapter` and exercised locally by `sandbox-idm-adapter`, which serves the pre-built bundle from an Express host and stubs the IDM/project APIs it needs to boot.

## Behavior

- The AngularJS SPA covers legacy Integrate data-manager screens: project browsing/editing, jobs, versioning, settings, reporting, monitoring, diagnostics.
- `sandbox-idm-adapter` serves the built adapter at `http://localhost:3001/` with well-known script URLs (`/milliman.idm.js`, `/milliman.idm.desktop.js`, `/milliman.idm.web.js`, `/milliman.jobs.js`, `/milliman.versioning.js`, etc.).
- The host stubs enough of the IDM gateway (`/api/idmapplication/configuration`, project edit endpoints, `/api/ifp/configuration`) to boot the AngularJS app without a real backend.
- OIDC auth for the legacy SPA is still handled inside the adapter's own `oidc-client@1.5.4` usage — independent of `@integrate-core-ui/auth`.
- This package is intentionally outside the pnpm workspace and uses its own `npm` + private `.npmrc`; it is published to `rainier-latest` via a separate pipeline path.

## Entry Points

- `packages/idm-adapter/index.js:1-5` — Node module exporting the built `index.html` path.
- `packages/idm-adapter/Gulpfile.js` — calls `build.app(['milliman.core', 'milliman.web'], ...)`.
- `apps/web/sandbox-idm-adapter/server.js:1` — Express host (port 3001).
- `apps/web/sandbox-idm-adapter/server.js:9` — static mount of `dist/packages/idm-adapter`.
- `apps/web/sandbox-idm-adapter/server.js:11`, `:17`, `:23`, `:29` — per-module script routes.
- `apps/web/sandbox-idm-adapter/server.js:35-59` — stubbed IDM/project/IFP API endpoints.
- `pipelines/validate.yml:92-112` — `IdmAdapter_Validate` (separate npm install + build).
- `pipelines/deploy.yml:54-87` — separate publish via `tools/publish-feed.ts`.

## Key Dependencies

- AngularJS 1.5.25 (via `@neverendingsupport/angularjs`).
- `@flowjs/ng-flow`, `bootstrap 3.4.1`, `carbon-components 10.48.0` (legacy), `codemirror 5.31`, `dagre`, `jquery 3.7.1`, `lodash 4.17.21`, `oidc-client 1.5.4`.
- Build: `gulp 4`, `webpack 1.15`, Karma/Jasmine (legacy test stack).
- `express 5.1.0` — sandbox host only.

## Related Features

- [ADO Validate Pipeline (dev)](../dev/ado-validate-pipeline.md) — runs the separate `IdmAdapter_Validate` job.
- [ADO Deploy Pipeline (dev)](../dev/ado-deploy-pipeline.md) — uses `tools/publish-feed.ts` for this package.
- [Sandbox Apps (dev)](../dev/sandbox-apps.md)

## Change Log

- 2026-04-21: Seeded.
