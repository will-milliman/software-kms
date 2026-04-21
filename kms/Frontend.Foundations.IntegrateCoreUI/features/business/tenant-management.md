# Tenant Management

- **Category:** business
- **Status:** active
- **Owners:** UI Platform Team (sandbox)

## Summary

`sandbox-tenant-manager` is a full admin SPA for Integrate's multi-tenant identity and access model. It lets operators manage tenants, environments, groups, roles, permissions, and assignments — and download client installers and connection strings.

## Behavior

- Users land on `/groups` by default (`src/routes/index.tsx:3`).
- **Groups page**: AG Grid of tenant groups with add, import (CSV/JSON), and delete actions.
- **Assignment page**: environment dropdown, group radios, and role checkboxes to wire groups to roles via `addGroupRole` / `deleteGroupRole` mutations.
- **Roles page**: role management with add/delete tools injected into the Toolbox; permission checkboxes trigger `updateRolePermission`. Non-ops admins never see `opsOnly` permissions; permissions are further filtered by the tenant's `tier`.
- **App Download page**: downloadable installer + release notes, with a copy-to-clipboard connection string.
- **Reports page**: embedded Power BI report (see separate feature).
- An admin check at load (`/api/services/tenant-manager/tenantmanager/admincheck`) returns `{ isOpsAdmin, isTenantAdmin }` and gates UI.
- The active tenant/environment/group/role is tracked in Redux (`tenantSlice`) and auto-seeded from the first API response (`tenant.slice.ts:19-33`).
- All API calls go through an RTK Query base query that injects a bearer `Authorization` header and resolves base URLs by key (`GATEWAY`, `IDM`, `PROJECT_MANAGER`, etc.).

## Entry Points

- `apps/web/sandbox-tenant-manager/src/main.tsx:21` — app bootstrap (Redux + TanStack Router + Modal + ICU Context + Theme).
- `apps/web/sandbox-tenant-manager/src/routes/__root.tsx:8` — shell layout.
- `apps/web/sandbox-tenant-manager/src/routes/groups.tsx:4`, `assignment.tsx:4`, `roles.tsx:4`, `app-download.tsx:4`, `reports.tsx:4`.
- `apps/web/sandbox-tenant-manager/src/pages/groups/groups.page.tsx:56`.
- `apps/web/sandbox-tenant-manager/src/pages/assignment/assignment.page.tsx:13`.
- `apps/web/sandbox-tenant-manager/src/pages/roles/roles.page.tsx:15`.
- `apps/web/sandbox-tenant-manager/src/pages/app-download/app-download.page.tsx:6`.
- `apps/web/sandbox-tenant-manager/src/store/tenant.api.ts:12` — RTK Query `createApi` (`reducerPath: 'gateway'`).
- `apps/web/sandbox-tenant-manager/src/store/endpoints/tenants.ts:17` — `useAdminCheckQuery` + permission filters (`:27`).
- `apps/web/sandbox-tenant-manager/src/store/models/tenant.ts:45` — `Tenant`, `TenantDetail`, `Group`, `Role`, `Permission` types.
- `apps/web/sandbox-tenant-manager/src/store/slices/base-url/base-url.slice.ts:36` — `dynamicBaseQuery` with bearer injection.

## Key Dependencies

- `@reduxjs/toolkit`, `react-redux` — state + RTK Query.
- `@tanstack/react-router` — hash-history file-based routing.
- `@integrate-core-ui/frame`, `grid`, `modal`, `store`, `themes` — shell, grid, modals, tab stores, theming.
- `msw` — API mocking in tests and dev (see MSW feature).
- `@carbon/react` — UI primitives.

## Related Features

- [App Shell Frame](./app-shell-frame.md)
- [Power BI Reports](./powerbi-reports.md)
- [Data Grid (dev)](../dev/data-grid.md)
- [MSW API Mocking (dev)](../dev/msw-api-mocking.md)
- [Sandbox Apps (dev)](../dev/sandbox-apps.md)

## Change Log

- 2026-04-21: Seeded.
