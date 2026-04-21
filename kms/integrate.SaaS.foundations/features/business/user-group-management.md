# User & Group Management API

- **Category:** business
- **Status:** active
- **Owners:** @milliman-lts/platform-engineering

## Summary

`Integrate.SaaS.AuthServer.Api` is a JwtBearer-protected REST API that manages platform users and groups backed by the `userindex` table and `auth-users` / `auth-userlist` blob containers. It is reached through the YARP ingress at `/users/**` and `/groups/**`.

## Behavior

- Resource-server JwtBearer validation (tokens issued by the Issuer).
- `Client/Middleware` pipeline (`ClaimsRequestContext`, `TenantMiddleware`, `EnvironmentMiddleware`, `UpdateRingMiddleware`, `AuthorizeMiddleware`) hydrates per-request context before hitting controllers.
- CRUD over users and groups (tenant-scoped); indexed lookups via `IndexedUserStore`.

## Entry Points

- `auth-server/src/Integrate.SaaS.AuthServer.Api/Program.cs:1-105` — host bootstrap.
- `auth-server/src/Integrate.SaaS.AuthServer.Api/Controllers/UsersController.cs` — user endpoints.
- `auth-server/src/Integrate.SaaS.AuthServer.Api/Controllers/GroupsController.cs` — group endpoints.
- `auth-server/src/Integrate.SaaS.AuthServer/Data/UserStore.cs`, `IndexedUserStore.cs` — persistence.

## Key Dependencies

- `Microsoft.AspNetCore.Authentication.JwtBearer` 10.0.2.
- `Integrate.SaaS.AuthServer.Client` middleware pipeline.
- Azure Blob + Table Storage.

## Related Features

- [OIDC Issuer](./oidc-issuer.md)
- [Tenant Administration](./tenant-administration.md)
- [YARP Ingress Gateway](../dev/yarp-ingress-gateway.md)

## Change Log

- 2026-04-21: Seeded.
