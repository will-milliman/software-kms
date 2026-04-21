# Environment & Ring Rollout

- **Category:** business
- **Status:** active
- **Owners:** @milliman-lts/platform-engineering

## Summary

Each tenant has one or more **Environments** (e.g. `dev`, `qa`, `prod`). Per environment, tenant admins can bind **groups** to **roles** (role map) and assign each group a **ring** (ring map), enabling progressive rollout of configuration and software updates per group.

## Behavior

- `GET Tenants/{tenantDomain}/Environments/{environmentName}` — returns the full `EnvironmentModel` (permission map, role map, ring map, custom permissions).
- `POST .../AddGroupRoles` / `.../DeleteGroupRoles` — maintain the group → role(s) mapping.
- `POST .../UpdateGroupRing` — assign a group to a ring (`stable`, beta, etc.).
- Commands audited via MediatR `AuditBehavior`.

## Entry Points

- `tenant-admin/src/Integrate.Gateway.TenantManager.Api/Controllers/EnvironmentsController.cs:15` — `Details` (L18), `AddGroupRoles` (L41), `DeleteGroupRoles` (L66), `UpdateGroupRing` (L91).
- `tenant-admin/src/Integrate.Gateway.TenantManager.Api/Commands/*` — `GroupRoleAddRequest`, `GroupRoleDeleteRequest`, `RingUpdateRequest`, `EnvironmentQueryRequest`.
- `tenant-admin/src/Integrate.Gateway.TenantManager.Api/Models/EnvironmentModel.cs:5-21` — `RoleMap`, `RingMap`, `PermissionMap`, `CustomPermissions`.
- `tenant-admin/src/Integrate.Gateway.TenantManager.Api/Data/EnvironmentStore.cs` — persistence.

## Key Dependencies

- MediatR + `AuditBehavior`.
- `EnvironmentStore` (Azure Blob).
- Downstream consumers: `ConfigurationService`, `UpdateService` (ring-partitioned).

## Related Features

- [Ring-Based Rollout](./ring-based-rollout.md)
- [Groups, Roles & Permissions](./groups-roles-permissions.md)
- [Execution Environments](./execution-environments.md)

## Change Log

- 2026-04-21: Seeded.
