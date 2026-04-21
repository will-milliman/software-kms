# MSW API Mocking

- **Category:** dev
- **Status:** active
- **Owners:** UI Platform Team (sandbox-tenant-manager)

## Summary

`sandbox-tenant-manager` uses MSW (Mock Service Worker) both in Vitest tests (Node `setupServer`) and in the browser (`setupWorker`) to intercept RTK Query traffic. A handler registry keyed by action name enables per-test overrides.

## Behavior

- **Browser mocks:** `src/utils/msw.ts:1` calls `setupWorker(...)`, started by `<WorkerProvider>` inside the root route (`src/routes/__root.tsx:11`). The service-worker URL is prefixed with the app's prod `base` path.
- **Test mocks:** `vitest.setup.ts:40` boots MSW with `setupServer` through `createMsw(handlers, setupServer, mswIdSpy)`. Global helpers `overrideApi` and `mswIdSpy` are exposed on `globalThis` for specs.
- **Handler registry:** `src/store/test/handlers.ts:14` maps keys to handlers: `ADMIN_CHECK`, `FETCH_GROUPS`, `ADD_ROLE`, `DELETE_ROLE`, `ADD_GROUP`, `DELETE_GROUP`, `ADD_GROUP_ROLE`, `DELETE_GROUP_ROLE`, `GET_TENANTS`, `GET_TENANT_DETAIL`, `GET_TENANT_ENV_DETAIL`, `UPDATE_ROLE_PERMISSIONS`.
- **Individual handlers:** `src/store/test/endpoints/*.ts`.
- **Fixtures:** `src/store/fixtures/{tenants,roles,permissions,groups,environments}.ts`.
- **Vite integration:** `vite.config.mts:104-116` defines a custom `mswBrowserPlugin` active only in `mode === 'test'` that rewrites `import {...} from "msw/browser"` to CJS `require` so Vitest's transform pipeline resolves it.
- **Auto-mock:** `src/utils/__mocks__/msw.ts` is used via `vi.mock('./src/utils/msw')` at `vitest.setup.ts:7`.
- Worker directory declared at root `package.json:115`: `apps\web\sandbox-tenant-manager\public`. Service worker file: `public/mockServiceWorker.js`.

## Entry Points

- `apps/web/sandbox-tenant-manager/src/utils/msw.ts:1` — browser worker.
- `apps/web/sandbox-tenant-manager/src/routes/__root.tsx:11` — `<WorkerProvider>`.
- `apps/web/sandbox-tenant-manager/vitest.setup.ts:1` — test bootstrap.
- `apps/web/sandbox-tenant-manager/vitest.setup.ts:40` — `createMsw(...)`.
- `apps/web/sandbox-tenant-manager/src/store/test/handlers.ts:14` — registry keys.
- `apps/web/sandbox-tenant-manager/src/store/test/create-msw.ts` — factory.
- `apps/web/sandbox-tenant-manager/src/store/fixtures/*.ts` — fixture data.
- `apps/web/sandbox-tenant-manager/vite.config.mts:104-116` — `mswBrowserPlugin`.
- `apps/web/sandbox-tenant-manager/public/mockServiceWorker.js` — SW file.

## Key Dependencies

- `msw@2.12.10`.
- `vitest@3.2.2` (jsdom), `@testing-library/react`, `@testing-library/jest-dom`.

## Related Features

- [Sandbox Apps](./sandbox-apps.md)
- [Tenant Management (business)](../business/tenant-management.md)

## Change Log

- 2026-04-21: Seeded.
