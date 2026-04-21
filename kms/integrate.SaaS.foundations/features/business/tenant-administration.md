# Tenant Administration

- **Category:** business
- **Status:** active
- **Owners:** @milliman-lts/platform-engineering (API) + @milliman-lts/frontend-platform-engineering (SPA)

## Summary

The `tenant-admin/` solution provides the API + React SPA through which TenantAdmins and OpsAdmins manage the metadata of every tenant on the Integrate platform: details, permission catalog, admin roster, and the capability bits the SPA should render.

## Behavior

- `GET api/services/tenant-manager/TenantManager/Index` — returns the caller's admin-capable tenants and the filtered permission catalog (tier and ops-only rules applied).
- `GET api/services/tenant-manager/TenantManager/AdminCheck` — reveals `IsOpsAdmin` / `IsTenantAdmin` flags for UI gating.
- `GET api/services/tenant-manager/Tenant/{tenantDomain}` — tenant details.
- Backed by JSON blobs in `auth-tenants` and driven through MediatR commands + HAL responses.
- The React SPA (`tenant-admin/src/ClientApp/`) consumes this API via RTK Query and renders pages with IBM Carbon.

## Entry Points

- `tenant-admin/src/Integrate.Gateway.TenantManager.Api/Controllers/TenantManagerController.cs:18` — `Index` (L21), `AdminCheck` (L42).
- `tenant-admin/src/Integrate.Gateway.TenantManager.Api/Controllers/TenantController.cs:14` — `Details` (L17), `UpdateTier` (L38).
- `tenant-admin/src/Integrate.Gateway.TenantManager.Api/Controllers/ConfigController.cs:11` — `milliman.config.js` (L17), `Data` (L40).
- `tenant-admin/src/Integrate.Gateway.TenantManager.Api/Program.cs:13,76` — host bootstrap.
- `tenant-admin/src/ClientApp/src/routes/` — SPA routes.
- `tenant-admin/src/ClientApp/src/store/api.ts:17-33` — RTK Query base client.

## Key Dependencies

- `MediatR` 10.0.1 — CQRS for commands/queries.
- `Milliman.Hal` 8.0.82 — HAL+JSON.
- `Microsoft.Identity.Web` 3.14.1 — JWT + Azure AD.
- `Azure.Storage.Blobs` — `TenantStore`, `AppStore`, `EnvironmentStore`.
- React 19, TanStack Router, RTK Query, Carbon.

## Related Features

- [Tenant Tier Management](./tenant-tier-management.md)
- [Groups, Roles & Permissions](./groups-roles-permissions.md)
- [Environment & Ring Rollout](./environment-ring-rollout.md)
- [Execution Environments](./execution-environments.md)
- [Embedded Power BI Reports](./embedded-powerbi-reports.md)

## Change Log

- 2026-04-21: Seeded.
