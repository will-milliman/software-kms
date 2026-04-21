# Data Protection & Ticket Store

- **Category:** dev
- **Status:** active
- **Owners:** @milliman-lts/platform-engineering

## Summary

Auth-server services share a consistent **Data Protection** setup: the ASP.NET DPAPI keyring is persisted to Azure Blob and encrypted at rest with an X.509 certificate. Large authentication cookies are offloaded to a **blob-backed `ITicketStore`** so HTTP cookies stay small.

## Behavior

- Each service has its own DPAPI container: `auth-dpapi` (IssuerService, Api), `portal-dpapi` (PortalService), `sample-dpapi` (SampleApp).
- Each service has its own session container: `auth-sessions`, `portal-sessions`, `sample-sessions`.
- Dev certs generated via mkcert (`.dpapi-certs/`) at AppHost startup; production certs come from Azure Key Vault / managed identity.
- `Integrate.SaaS.AuthServer.Client.Authentication.TicketStore` implements `ITicketStore` over Azure Blob.
- `auth-server/docs/AUTHENTICATION_COOKIE_CONFIGURATION.md` is the canonical reference for cookie properties, expiration, security, localhost behavior, DevTools visibility, and troubleshooting.

## Entry Points

- `auth-server/src/Integrate.SaaS.AuthServer.Client/Authentication/TicketStore.cs`.
- `auth-server/src/Integrate.SaaS.AuthServer.IssuerService/Program.cs:1-101` — DPAPI + cookie wiring.
- `auth-server/src/Integrate.SaaS.AuthServer.PortalService/Program.cs:1-158` — DPAPI + ticket store wiring.
- `auth-server/docs/AUTHENTICATION_COOKIE_CONFIGURATION.md` — ~3100-line guide.
- `auth-server/src/Integrate.SaaS.AuthServer.AppHost/DevCertificateExtensions.cs` — dev certs.

## Key Dependencies

- `Microsoft.AspNetCore.DataProtection.AzureStorage`.
- `Microsoft.AspNetCore.DataProtection.Extensions`.
- mkcert (dev).

## Related Features

- [mkcert Dev Certificates](./mkcert-dev-certificates.md)
- [Admin Portal](../business/admin-portal.md)

## Change Log

- 2026-04-21: Seeded.
