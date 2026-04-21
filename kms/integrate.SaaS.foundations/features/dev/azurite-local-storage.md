# Azurite Local Storage

- **Category:** dev
- **Status:** active
- **Owners:** @milliman-lts/platform-engineering

## Summary

All services use **Azure Storage** exclusively for persistence. In local dev, Aspire AppHosts launch the **Azurite** emulator (blob/table/queue) with a persistent volume and auto-create the required containers, tables, and queues.

## Behavior

- Azurite runs on ports 10000 (blob) / 10001 (queue) / 10002 (table).
- Connection string `devstoreaccount1` (well-known Azurite default) wired into services via Aspire references.
- Seed data files (e.g. `tenant-admin/src/Integrate.Gateway.TenantManager.AppHost/.test-data/auth-tenants/...`) uploaded on resource-ready.
- Container / table / queue catalogue — see `architecture.md`: `auth-tenants`, `auth-clients`, `auth-userlist`, `auth-users`, `auth-codes`, `auth-refresh`, `auth-dpapi`, `auth-sessions`, `configuration-store`, `update-downloads`, `portal-sessions`, `portal-dpapi`, `sample-dpapi`, `auth-auditlog`, tables `userindex` + `auditlog`, queues `audit-events` + `audit-events-dl`.

## Entry Points

- `tenant-admin/src/Integrate.Gateway.TenantManager.AppHost/AppHost.cs:17-31` — Azurite registration.
- `audit-log/src/Integrate.SaaS.AuditLog.AppHost/AppHost.cs:6-22` — Azurite + queue/table creation.
- `auth-server/src/Integrate.SaaS.AuthServer.AppHost/DevelopmentSeedData.cs:115-129` — 14 blob containers + 2 tables + 2 queues.
- `audit-log/src/Integrate.SaaS.AuditLog.ServiceDefaults/AzureStorage.cs:5-23` — production vs dev credential selection.

## Key Dependencies

- `Aspire.Hosting.Azure.Storage` 9.4.2 / 13.0.2 / 13.1.0.
- `mcr.microsoft.com/azure-storage/azurite`.

## Related Features

- [Aspire AppHost Orchestration](./aspire-apphost-orchestration.md)
- [Aspire Hosting Testing](./aspire-hosting-testing.md)

## Change Log

- 2026-04-21: Seeded.
