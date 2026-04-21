# Tenant Configuration Service

- **Category:** business
- **Status:** active
- **Owners:** @milliman-lts/platform-engineering

## Summary

`Integrate.SaaS.AppServices.ConfigurationService` is a JwtBearer-protected API that returns tenant-scoped configuration partitioned by **ring** and **environment**. Downstream client applications fetch their runtime configuration from this service via the ingress at `/configuration/**`.

## Behavior

- Scope and tenant claims checked by the `Client/Middleware` pipeline (`ClaimsRequestContext`, `TenantMiddleware`, `EnvironmentMiddleware`, `UpdateRingMiddleware`).
- Configuration is keyed by `UpdateRingKey` (tenant + environment + ring).
- Data persisted in the `configuration-store` blob container.

## Entry Points

- `auth-server/src/Integrate.SaaS.AppServices.ConfigurationService/Program.cs:1-88` — host bootstrap.
- `auth-server/src/Integrate.SaaS.AppServices.ConfigurationService/Models/UpdateRingKey.cs` — key type.
- Ingress route `config` — `auth-server/src/Integrate.SaaS.AuthServer.Ingress/appsettings.json:10-128`.

## Key Dependencies

- `Microsoft.AspNetCore.Authentication.JwtBearer`.
- `Integrate.SaaS.AuthServer.Client`.
- Azure Blob Storage.

## Related Features

- [Ring-Based Rollout](./ring-based-rollout.md)
- [Software Update Distribution](./software-update-distribution.md)
- [YARP Ingress Gateway](../dev/yarp-ingress-gateway.md)

## Change Log

- 2026-04-21: Seeded.
