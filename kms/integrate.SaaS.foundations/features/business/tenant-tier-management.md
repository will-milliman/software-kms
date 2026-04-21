# Tenant Tier Management

- **Category:** business
- **Status:** active
- **Owners:** @milliman-lts/platform-engineering

## Summary

OpsAdmins can change a tenant's commercial **tier** (an integer). The tier gates which permissions are visible in the permission catalog — only permissions with `Tier <= TenantModel.Tier` (and not `OpsOnly` for non-ops users) are returned to tenant admins.

## Behavior

- `POST api/services/tenant-manager/Tenant/{tenantDomain}/UpdateTier` — OpsAdmin-only.
- Dispatched via MediatR `TenantTierUpdateRequest`; handler writes the updated `TenantModel` back to `{tenantDomain}/tenant.json` in the `auth-tenants` container.
- `AuditBehavior` records the change in the audit blob.

## Entry Points

- `tenant-admin/src/Integrate.Gateway.TenantManager.Api/Controllers/TenantController.cs:38` — `UpdateTier` action (OpsAdmin policy).
- `tenant-admin/src/Integrate.Gateway.TenantManager.Api/Commands/TenantTierUpdateRequest.cs` — command.
- `tenant-admin/src/Integrate.Gateway.TenantManager.Api/Handlers/` — matching handler.
- `tenant-admin/src/Integrate.Gateway.TenantManager.Api/Models/PermissionModel.cs` — `Tier`, `OpsOnly` gating.

## Key Dependencies

- MediatR + `AuditBehavior<,>` pipeline.
- `TenantStore` (Azure Blob).
- Authorization policy `OpsAdmin`.

## Related Features

- [Tenant Administration](./tenant-administration.md)
- [Groups, Roles & Permissions](./groups-roles-permissions.md)
- [Audit Logging](./audit-logging.md)

## Change Log

- 2026-04-21: Seeded.
