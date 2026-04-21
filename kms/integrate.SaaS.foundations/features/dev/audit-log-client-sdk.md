# Audit-Log Client SDK

- **Category:** dev
- **Status:** active
- **Owners:** @milliman-lts/platform-engineering

## Summary

`Integrate.SaaS.AuditLog.Client` is a **NuGet-packaged SDK** with broad framework compatibility (`net10.0;net9.0;net6.0`) that any .NET service can consume to emit `AuditEvent`s through an HTTP sidecar. The SDK handles HttpClient configuration (service discovery, resilience) and provides a no-op fallback (`DisabledAuditLogClient`) when auditing is disabled.

## Behavior

- `AddAuditLogClient(IServiceCollection, Action<AuditLogClientOptions>?)` registers a typed `HttpClient` named `audit-log-sidecar` pointing at `{Scheme}://{Hostname}:{Port=5002}`.
- `IAuditLog.LogEventAsync(AuditEvent)` serializes JSON and POSTs to `/api/v1/audit`, then `EnsureSuccessStatusCode`s.
- When `Enabled = false`, the DI container resolves `DisabledAuditLogClient` — a zero-op — so unit tests don't need to stand up the sidecar.
- Published from Azure DevOps pipeline `audit-log/src/.pipelines/client.build.yaml` on `main` or `forcePush`.

## Entry Points

- `audit-log/src/Integrate.SaaS.AuditLog.Client/AuditLogClient.cs:7,10`.
- `audit-log/src/Integrate.SaaS.AuditLog.Client/ServiceCollectionExtensions.cs:7` — `AddAuditLogClient`.
- `audit-log/src/Integrate.SaaS.AuditLog.Client/Constants.cs:5-6` — `AuditLogClientHttpClientName`, endpoint path.
- `audit-log/src/Integrate.SaaS.AuditLog.Client/AuditEvent.cs` — client-side classes (mutable, net6/9-friendly).
- `audit-log/src/Integrate.SaaS.AuditLog.Client/DisabledAuditLogClient.cs` — no-op.

## Key Dependencies

- `Microsoft.Extensions.Http.Resilience` 10.0.0.
- `Microsoft.Extensions.ServiceDiscovery` 10.0.0.

## Related Features

- [Audit Logging](../business/audit-logging.md)
- [Audit Logging (Dev)](./audit-logging.md)

## Change Log

- 2026-04-21: Seeded.
