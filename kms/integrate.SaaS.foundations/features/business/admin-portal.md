# Admin Portal

- **Category:** business
- **Status:** active
- **Owners:** @milliman-lts/platform-engineering

## Summary

`Integrate.SaaS.AuthServer.PortalService` is an ASP.NET Core MVC web UI for platform administrators, reached via the ingress at `/portal/**` and the OIDC callback `/signin-portal/**`. It authenticates end-users with OIDC code flow against the Issuer and stores large session payloads in a blob-backed ticket store.

## Behavior

- OIDC code-flow login (Issuer as authority).
- Cookie authentication with **blob-backed ticket store** (`Client/Authentication/TicketStore.cs`) — cookies stay small, ticket bodies live in `portal-sessions` blob.
- Cert-based Data Protection keyring persisted to `portal-dpapi` blob container.
- OWASP security headers applied via `UseIntegrateSecurityHeaders()`.

## Entry Points

- `auth-server/src/Integrate.SaaS.AuthServer.PortalService/Program.cs:1-158` — host bootstrap (OIDC + cookie + DPAPI + ticket store).
- `auth-server/src/Integrate.SaaS.AuthServer.PortalService/Controllers/` — MVC controllers.
- `auth-server/src/Integrate.SaaS.AuthServer.PortalService/Views/` — Razor views.

## Key Dependencies

- `Microsoft.AspNetCore.Authentication.OpenIdConnect` + cookie auth.
- `Integrate.SaaS.AuthServer.Client` — `TicketStore`, middlewares, builder extensions.
- Azure Blob Storage (`portal-sessions`, `portal-dpapi`).

## Related Features

- [OIDC Issuer](./oidc-issuer.md)
- [Data Protection & Ticket Store](../dev/data-protection-and-ticket-store.md)

## Change Log

- 2026-04-21: Seeded.
