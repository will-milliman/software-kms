# Aspire Hosting Testing

- **Category:** dev
- **Status:** active
- **Owners:** @milliman-lts/platform-engineering

## Summary

Integration tests spin up the **entire distributed application** (Azurite + all sub-services) via `Aspire.Hosting.Testing`, then exercise the system through real HTTP and Azure Storage clients — no mocking of the transport layer.

## Behavior

- `DistributedApplicationTestingBuilder.CreateAsync<Projects.Integrate_SaaS_AuditLog_AppHost>()` constructs the app graph.
- Tests wait for resources (e.g. `TableStorage`, `audit-log-sidecar`, `audit-log-service`) to reach `Healthy` state.
- `[assembly: CaptureConsole]` on xunit.v3 preserves service logs in the test output.
- In tenant-admin the test project references the `AppHost` csproj and uses `Aspire.Hosting.ApplicationModel` (global using).

## Entry Points

- `audit-log/src/Integrate.SaaS.AuditLog.IntegrationTests/IntegrationTest1.cs:12,34-36` — currently skipped in CI (`Skip = "Skipped: Client not able to connect to the sidecar in the test environment"`).
- `audit-log/src/Integrate.SaaS.AuditLog.IntegrationTests/Assembly.cs:1` — `CaptureConsole`.
- `tenant-admin/src/Integrate.Gateway.TenantManager.Api.Tests/*.csproj:42` — Aspire global using.
- `auth-server/src/Integrate.SaaS.AuthServer.IssuerService.Tests/*` — `Microsoft.AspNetCore.TestHost` + `Microsoft.Extensions.Diagnostics.Testing`.

## Key Dependencies

- `Aspire.Hosting.Testing` 13.0.2 / 13.1.0.
- `xunit.v3` 3.2.x.
- `Testcontainers.Azurite`.

## Related Features

- [Aspire AppHost Orchestration](./aspire-apphost-orchestration.md)
- [Azurite Local Storage](./azurite-local-storage.md)

## Change Log

- 2026-04-21: Seeded.
