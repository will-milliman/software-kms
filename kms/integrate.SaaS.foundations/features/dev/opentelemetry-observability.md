# OpenTelemetry Observability

- **Category:** dev
- **Status:** active
- **Owners:** @milliman-lts/platform-engineering

## Summary

Every .NET service emits **OpenTelemetry** traces, metrics, and logs, wired up centrally through each service-defaults project. Exporters target the OTLP protocol (Aspire dashboard in dev) and **Azure Monitor** in production.

## Behavior

- `ServiceDefaults.Extensions.AddServiceDefaults()` registers:
  - OpenTelemetry AspNetCore + HttpClient + Runtime instrumentation.
  - Environment / Host / Container / Process / ProcessRuntime / Azure resource detectors.
  - OTLP exporter (`OpenTelemetry.Exporter.OpenTelemetryProtocol` 1.13.1).
  - `Azure.Monitor.OpenTelemetry.AspNetCore` 1.3.0 for production Application Insights.
  - Health checks `/health`, `/alive` with request timeouts + output cache + `HealthCheckPolicyName`.
  - Service discovery + standard resilience handler on every HttpClient.
- Custom **audit-log semantic conventions** defined in `audit-log/src/Integrate.SaaS.AuditLog.ServiceDefaults/SemanticConventions.cs` (`auditlog.service.name`, `auditlog.event.id|type|timestamp|user.id|...`).
- Custom resource dimensions wired via `CustomDimensionsExtensions` / `CustomDimensionsResourceBuilderExtensions` / `CustomDimensionsDetector`.

## Entry Points

- `audit-log/src/Integrate.SaaS.AuditLog.ServiceDefaults/Extensions.cs:1-172`.
- `auth-server/src/Integrate.SaaS.AuthServer.ServiceDefaults/Extensions.cs` — same pattern.
- `tenant-admin/src/Integrate.Gateway.TenantManager.ServiceDefaults/*` — same pattern.
- `audit-log/src/Integrate.SaaS.AuditLog.ServiceDefaults/SemanticConventions.cs` — auditlog tag names.
- `audit-log/src/Integrate.SaaS.AuditLog.Sidecar/AuditEventEndpoint.cs:12-14` — activity tagging.

## Key Dependencies

- `OpenTelemetry.*` 1.13.x.
- `Azure.Monitor.OpenTelemetry.AspNetCore` 1.3.0.

## Related Features

- [Aspire AppHost Orchestration](./aspire-apphost-orchestration.md)

## Change Log

- 2026-04-21: Seeded.
