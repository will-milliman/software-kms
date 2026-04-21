# Audit Logging (Business)

- **Category:** business
- **Status:** active
- **Owners:** @milliman-lts/platform-engineering

## Summary

Every meaningful platform action (tenant/group/role/permission change, token issuance, webhook dispatch, etc.) is recorded in an **append-only audit log** for compliance, forensics, and support. Records are immutable and query-efficient (newest-first via descending row keys).

## Behavior

- **Tenant-admin** path: every MediatR command is intercepted by `AuditBehavior<,>` and appended to an Azure **append blob** in the `auth-auditlog` container. Blobs roll over at 49,000 events per ADR001.
- **Cross-service** path: services call `IAuditLog.LogEventAsync` from the `Integrate.SaaS.AuditLog.Client` NuGet SDK, which POSTs to the audit-log Sidecar, which enqueues to Azure Queue `audit-events`; the audit-log Service persists each event to Azure Table `auditlog` (PK=ServiceName, RK=descending ticks). Poison messages go to `audit-events-dl`.
- Canonical record: `AuditEvent` (`ServiceName`, `EventId`, `EventType`, `Actor`, `Request`, `Outcome`, `OccurredAt`, `Details`, `GroupClaims`, `PermissionClaims`).

## Entry Points

- `audit-log/src/Integrate.SaaS.AuditLog.Api/AuditEvent.cs:3-34` — canonical record.
- `tenant-admin/src/Integrate.Gateway.TenantManager.Api/AuditLog/AudiLogExtensions.cs:17` — `AuditBehavior<,>` registration.
- `tenant-admin/src/Integrate.Gateway.TenantManager.Api/AuditLog/RollingAuditBlobLogger.cs:42-78` — append blob + rollover.
- `tenant-admin/docs/decisions/ADR001 - audit log.md` — decision record.

## Key Dependencies

- `MediatR` pipeline (tenant-admin).
- `Integrate.SaaS.AuditLog.Client` NuGet (consumers).
- Azure Append Blob / Azure Queue / Azure Table.

## Related Features

- [Audit Logging (Dev)](../dev/audit-logging.md)
- [Audit-Log Client SDK](../dev/audit-log-client-sdk.md)

## Change Log

- 2026-04-21: Seeded.
