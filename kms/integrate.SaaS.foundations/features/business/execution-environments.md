# Execution Environments

- **Category:** business
- **Status:** active
- **Owners:** @milliman-lts/platform-engineering

## Summary

An **ExecutionEnvironment** represents a runtime compute environment assigned to a tenant (each identified by a GUID). Tenant-admin exposes read endpoints for discovering execution environments and their per-execution-environment permission summary.

## Behavior

- `GET tenants/ExecutionEnvironments/{executionEnvironmentId}` — summary for a single execution environment.
- `GET tenants/ExecutionEnvironments/` — list for the current caller.
- Permissions surface is filtered by the execution-environment claim on the caller's token.

## Entry Points

- `tenant-admin/src/Integrate.Gateway.TenantManager.Api/Controllers/ExecutionEnvironmentsController.cs:13` — `Summary` (L16), `List` (L59).
- `tenant-admin/src/Integrate.Gateway.TenantManager.Api/Commands/ExecutionEnvironmentQueryRequest.cs` — command.
- `tenant-admin/src/Integrate.Gateway.TenantManager.Api/Data/EnvironmentStore.cs` — `IExecutionEnvironmentStore` implementation.
- `tenant-admin/src/Integrate.Gateway.TenantManager.Api/Models/ExecutionEnvironmentModel.cs` — model.

## Key Dependencies

- MediatR.
- `EnvironmentStore` (Azure Blob).
- `environment` JWT claim.

## Related Features

- [Environment & Ring Rollout](./environment-ring-rollout.md)
- [Tenant Administration](./tenant-administration.md)

## Change Log

- 2026-04-21: Seeded.
