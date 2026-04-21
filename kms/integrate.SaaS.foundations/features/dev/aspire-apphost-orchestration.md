# Aspire AppHost Orchestration

- **Category:** dev
- **Status:** active
- **Owners:** @milliman-lts/platform-engineering

## Summary

Each sub-service ships with its own **.NET Aspire AppHost** that wires up local dependencies (Azurite, Keycloak, audit-log sidecar, seed data, dev certs) and the sub-service's own projects for one-command local dev.

## Behavior

- `aspire run` from `.aspire/` (or project) launches Azurite + all projects; the Aspire dashboard surfaces telemetry and logs.
- AppHosts use parameters for configurable URLs / client secrets (AuthServer AppHost).
- Azurite volumes (`tenant-admin`, `issuer`) persist across restarts.
- Seed data (JSON blob files) is pushed into Azurite on resource-ready.

## Entry Points

- `auth-server/src/Integrate.SaaS.AuthServer.AppHost/AppHost.cs:1-282`.
- `tenant-admin/src/Integrate.Gateway.TenantManager.AppHost/AppHost.cs:4,68`.
- `audit-log/src/Integrate.SaaS.AuditLog.AppHost/AppHost.cs:4,30,37`.
- `auth-server/.aspire/settings.json` — pins the default AppHost project.
- `auth-server/src/Integrate.SaaS.AuthServer.AppHost/DevelopmentSeedData.cs:115-129` — seed.
- `auth-server/src/Integrate.SaaS.AuthServer.AppHost/DevCertificateExtensions.cs` — mkcert dev certs.

## Key Dependencies

- `Aspire.AppHost.Sdk` 13.0.2 / 13.1.0.
- `Aspire.Hosting.AppHost`, `Aspire.Hosting.Azure.Storage`, `Aspire.Hosting.Keycloak`.

## Related Features

- [Azurite Local Storage](./azurite-local-storage.md)
- [Keycloak Dev IdP](./keycloak-dev-idp.md)
- [mkcert Dev Certificates](./mkcert-dev-certificates.md)
- [Aspire Hosting Testing](./aspire-hosting-testing.md)

## Change Log

- 2026-04-21: Seeded.
