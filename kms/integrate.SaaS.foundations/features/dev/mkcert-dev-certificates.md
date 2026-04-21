# mkcert Dev Certificates

- **Category:** dev
- **Status:** active
- **Owners:** @milliman-lts/platform-engineering

## Summary

Auth-server's AppHost generates **locally-trusted X.509 certificates** with mkcert for two purposes:

1. **Token signing** (`.token-certs/`) — JWT signing keys for the Issuer in dev.
2. **DPAPI key encryption** (`.dpapi-certs/`) — encrypts the ASP.NET Data Protection keyring stored in blob.

## Behavior

- Certs are (re)generated on AppHost startup if missing.
- Both services load the cert from disk in dev and mount it into the container.
- Production uses Azure-managed certs (Key Vault / managed identity), not mkcert.

## Entry Points

- `auth-server/src/Integrate.SaaS.AuthServer.AppHost/DevCertificateExtensions.cs` — cert generation.
- `auth-server/src/Integrate.SaaS.AuthServer.AppHost/.token-certs/` — generated signing certs.
- `auth-server/src/Integrate.SaaS.AuthServer.AppHost/.dpapi-certs/` — generated DPAPI certs.
- `auth-server/src/Integrate.SaaS.AuthServer/DependencyInjection/OpenIdIssuerExtensions.cs:1-90` — where the Issuer consumes the signing cert.

## Key Dependencies

- `mkcert` (installed in the AppHost base image — `auth-server/src/Dockerfile`).

## Related Features

- [Aspire AppHost Orchestration](./aspire-apphost-orchestration.md)
- [Data Protection & Ticket Store](./data-protection-and-ticket-store.md)

## Change Log

- 2026-04-21: Seeded.
