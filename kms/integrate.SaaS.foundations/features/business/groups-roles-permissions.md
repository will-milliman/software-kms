# Groups, Roles & Permissions

- **Category:** business
- **Status:** active
- **Owners:** @milliman-lts/platform-engineering

## Summary

TenantAdmins manage three related concepts per tenant:

1. **Groups** — AAD/upstream-IdP group IDs mapped to display names.
2. **Roles** — named bundles of permissions.
3. **Role → Permission maps** — which permissions each role confers.

## Behavior

- `POST Tenants/{tenantDomain}/Groups/Add` / `.../Delete` — maintain the group ID → name dictionary.
- `POST Tenants/{tenantDomain}/Roles/Add` / `.../Delete` / `.../UpdatePermissions` — maintain the role set and their permission sets.
- All mutations go through MediatR `GroupAddRequest`, `GroupDeleteRequest`, `RoleAddRequest`, `RoleDeleteRequest`, `RolePermissionUpdateRequest` with `AuditBehavior` capturing each change.
- Authorization policy `TenantAdmin` applies.

## Entry Points

- `tenant-admin/src/Integrate.Gateway.TenantManager.Api/Controllers/GroupsController.cs:15` — `Add` (L18), `Delete` (L42).
- `tenant-admin/src/Integrate.Gateway.TenantManager.Api/Controllers/RolesController.cs:16` — `Add` (L19), `Delete` (L43), `UpdatePermissions` (L67).
- `tenant-admin/src/Integrate.Gateway.TenantManager.Api/Commands/*` — commands.
- `tenant-admin/src/Integrate.Gateway.TenantManager.Api/Handlers/*` — handlers.
- `tenant-admin/src/Integrate.Gateway.TenantManager.Api/Models/TenantModel.cs:5-20` — `Groups`, `Roles`, `PermissionMap`.

## Key Dependencies

- MediatR pipeline.
- `TenantStore` (Azure Blob).
- `TenantAdmin` policy.

## Related Features

- [Environment & Ring Rollout](./environment-ring-rollout.md)
- [Tenant Administration](./tenant-administration.md)
- [Audit Logging](./audit-logging.md)

## Change Log

- 2026-04-21: Seeded.
