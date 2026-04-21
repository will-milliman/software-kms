# Audit Logging (Dev)

- **Category:** dev
- **Status:** active
- **Owners:** @milliman-lts/platform-engineering

## Summary

Developer-facing view of how audit logging is wired up in both flavors that exist in the repo:

1. **Tenant-admin (in-process append blob)** — MediatR pipeline behavior writes every command to an Azure append blob with rollover.
2. **Audit-log service (out-of-process queue → table)** — Client SDK ➜ Sidecar ➜ Queue ➜ BackgroundService ➜ Table, with a dead-letter queue and idempotent writes.

## Behavior

### Tenant-admin path

- `AuditBehavior<,>` is registered as an `IPipelineBehavior<,>` (`AudiLogExtensions.cs:17`). It surrounds every MediatR `Send` with before/after hooks that write to the blob via `RollingAuditBlobLogger`.
- `RollingAuditBlobLogger.cs:42-78` manages the 49,000-event rollover threshold (`Constants.cs:60`) — when the current append blob hits the cap, a new one is created with a timestamp suffix.
- ADR001 (`tenant-admin/docs/decisions/ADR001 - audit log.md`) documents the choice of append blob over App Insights / Sentinel / EventHub (future candidate).

### Audit-log service path

- **Sidecar** (`Program.cs:40`) accepts `POST /api/v1/audit` → `QueueMessageFactory.CreateAsync` → Base64(JSON) → enqueue to `audit-events` queue (202 Accepted).
- **Service** (`AuditEventQueueService` BackgroundService) polls in batches (`MaxMessagesPerBatch=32`, visibility `1m`).
  - `QueueMessageParser` decodes; `null`/invalid → `audit-events-dl` + delete.
  - `DequeueCount > MaxDequeueCount(=5)` → `audit-events-dl` + delete.
  - HTTP 409 duplicate → delete (idempotent).
  - Else: `AuditLogRepository.StoreAsync` writes to `auditlog` Table (PK=ServiceName, RK=descending ticks).
- Dev integration test (`IntegrationTest1.cs:12`) is currently skipped in CI.

## Entry Points

- `audit-log/src/Integrate.SaaS.AuditLog.Api/AuditEvent.cs:3-34` — canonical record.
- `audit-log/src/Integrate.SaaS.AuditLog.Sidecar/AuditEventEndpoint.cs:10`.
- `audit-log/src/Integrate.SaaS.AuditLog.Sidecar/QueueMessageFactory.cs:14-18`.
- `audit-log/src/Integrate.SaaS.AuditLog.Service/AuditEventQueueService.cs:5-7`.
- `audit-log/src/Integrate.SaaS.AuditLog.Service/QueueMessageHandler.cs:17-34`.
- `audit-log/src/Integrate.SaaS.AuditLog.Service/EntityBuilder.cs:30-33`.
- `audit-log/src/Integrate.SaaS.AuditLog.Service/ServiceOptions.cs` — `QueueName`, `DeadLetterQueueName`, `TableName=auditlog`, `MaxDequeueCount=5`, `PollInterval=5s`, etc.
- `tenant-admin/src/Integrate.Gateway.TenantManager.Api/AuditLog/AudiLogExtensions.cs:17`.
- `tenant-admin/src/Integrate.Gateway.TenantManager.Api/AuditLog/RollingAuditBlobLogger.cs:42-78`.
- `tenant-admin/docs/decisions/ADR001 - audit log.md`.

## Key Dependencies

- `Azure.Storage.Queues` 12.24.0.
- `Azure.Data.Tables` 12.11.0.
- `MediatR` (tenant-admin pipeline).
- `Integrate.SaaS.AuditLog.Client` SDK.

## Related Features

- [Audit Logging (Business)](../business/audit-logging.md)
- [Audit-Log Client SDK](./audit-log-client-sdk.md)

## Change Log

- 2026-04-21: Seeded.
