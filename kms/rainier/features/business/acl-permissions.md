# ACL / Permissions

- **Category:** business
- **Status:** active
- **Owners:** authoring-and-governance

## Summary

Access control for Integrate projects and other resources. Uses a resource-based model with three permissions — Owner, Read, Write — granted to principals (users or groups). Currently used primarily for project-level access.

## Behavior

- On resource creation, an ACL is created with an initial owner.
- Owners can grant Read/Write to other principals.
- `ManageModelOwner` is a separate claim required to add/remove owners.
- A cached "no-owner projects" list flags orphaned resources.
- Real-time permission changes push to clients via SignalR `/acl/hub` (AzureSignalR in prod).
- The feature toggle `DisableManageModelOwner` can turn off owner management at the system level.

## Entry Points

- Service: `src/Rainier.Acl/` — ASP.NET Core service, event-sourced.
  - Bootstrap: `src/Rainier.Acl/src/Rainier.Acl/Program.cs:17`.
  - Startup: `.../Startup.cs:37`.
  - Hub: `.../Infrastructure/AclHub.cs` → `/acl/hub`.
  - Authorization: `.../Authorization/PermissionHandler.cs` + `PermissionRequirement.cs`; `"Read"` policy at `Startup.cs:98`.
- REST endpoints (`src/Rainier.Acl/src/Rainier.Acl/Controllers/AclController.cs`):
  - `:65` `GET /api/acl`, `:70` `POST /api/acl/create`.
  - `:93` `GET /api/acl/read/{resource}`, `:138` `GET /api/acl/list`.
  - `:129` `GET /api/acl/owner/{resource}` (requires `ManageModelOwner`).
  - `:157` `GET /api/acl/readPermissionChange/{resource}`.
  - `:183, :239` `POST /api/acl/addOwner|removeOwner/{resource}`.
  - `:278, :291` `POST /api/acl/addOrUpdatePermission|removePermission/{resource}`.
  - `:309` `GET /api/acl/principalResourcePermissions`.
- Projects view: `.../Controllers/ProjectsController.cs:84` `POST /api/projects/readmanywithpermissions`.

## Key Dependencies

- `ES.Core` — event-sourced store.
- `AzureSignalR` (prod).
- `Castle Windsor` DI.
- `Rainier.ServiceToolkit` JWT + health + well-known endpoints.
- Consumed by ProjectManager via Polly-wrapped HTTP client.

## Related Features

- [Release & Promote (IFP/IDM)](release-and-promote.md)
- [Governance](governance.md)
- [Project Authoring](project-authoring.md)

## Change Log

- 2026-04-21: Seeded.
