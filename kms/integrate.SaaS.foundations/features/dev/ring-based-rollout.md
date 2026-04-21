# Ring-Based Rollout (Dev)

- **Category:** dev
- **Status:** active
- **Owners:** @milliman-lts/platform-engineering

## Summary

Developer-facing view of ring-based rollout: how ring claims propagate from the Issuer through the client-middleware pipeline and partition downstream responses.

## Behavior

- **Claim emission**: Issuer sets the `http://schemas.milliman.com/identity/claims/ring` claim on tokens based on the caller's group + environment `RingMap`. Default `"stable"` (`Constants.cs:21`).
- **Middleware**: `UpdateRingMiddleware` in `Integrate.SaaS.AuthServer.Client` resolves the ring from claims and stores it on the request context; `AuthorizeMiddleware` rejects requests with no ring in services that require one.
- **Partitioning**: `UpdateRingKey` (tenant + environment + ring) is the logical partition key for `ConfigurationService` and `UpdateService` storage lookups — different rings can be served different blob payloads.
- **Tests**: `Client.Tests` covers middleware ordering; per-service tests exercise ring-scoped reads.

## Entry Points

- `auth-server/src/Integrate.SaaS.AuthServer/Constants.cs:21` — `DefaultUpdateRing`.
- `auth-server/src/Integrate.SaaS.AuthServer.Client/Middleware/UpdateRingMiddleware.cs`.
- `auth-server/src/Integrate.SaaS.AuthServer.Client/Middleware/ClaimsRequestContext.cs`.
- `auth-server/src/Integrate.SaaS.AppServices.ConfigurationService/Models/UpdateRingKey.cs`.
- `tenant-admin/src/Integrate.Gateway.TenantManager.Api/Commands/RingUpdateRequest.cs`.

## Key Dependencies

- Custom claim type constants (auth-server).
- `Integrate.SaaS.AuthServer.Client` middleware stack.

## Related Features

- [Ring-Based Rollout (Business)](../business/ring-based-rollout.md)
- [Tenant Configuration Service](../business/tenant-configuration-service.md)
- [Software Update Distribution](../business/software-update-distribution.md)

## Change Log

- 2026-04-21: Seeded.
