# RequiresPermission Authorization

- **Category:** dev
- **Status:** active
- **Owners:** backend-platform / acl

## Summary

`[RequiresPermission(...)]` attribute plus claims-inspection middleware gate MediatR commands and REST endpoints against the ACL service's permission model (Owner/Read/Write on a resource).

## Behavior

- Attribute declares the required permission and (optionally) the resource ID expression.
- Middleware in the [MediatR CQRS Pipeline](mediatr-cqrs.md) resolves the resource, calls the ACL service, and either allows the handler to run or returns 403.
- For read-heavy endpoints the permission check result is cached for the duration of the request.

## Entry Points

- `src/server/Rainier.Server.Core/Authorization/RequiresPermissionAttribute.cs`.
- Permission-check middleware alongside the attribute.

## Key Dependencies

- `Rainier.Acl` service.
- [JWT Auth (Online + Offline)](jwt-auth.md).

## Related Features

- [ACL / Permissions](../business/acl-permissions.md)
- [MediatR CQRS Pipeline](mediatr-cqrs.md)

## Change Log

- 2026-04-21: Seeded.
