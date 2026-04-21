# Cloud Run Storage (Origin)

- **Category:** business
- **Status:** active
- **Owners:** calculation-and-execution / projection-execution

## Summary

When projections execute in the cloud, **Rainier.Origin** hands out short-lived Azure Storage SAS tokens and URIs so clients (or grid workers) can upload model inputs, post request messages, and download outputs without holding long-term Azure credentials.

## Behavior

- Before a cloud run, the client requests a `JobStorageConfiguration` for the given `jobId` — this contains SAS URIs for input/output/cancel/request queues, reference data, and cached input.
- Clients upload inputs to the input blob container, then post a request to the request queue; output flows to the output container.
- A hosted cleanup service prunes expired input cache / containers.
- Portal details endpoint surfaces the job portal URL used by the Integrate UI to link back to a web view.

## Entry Points

- `src/Rainier.Origin/Origin/Program.cs:14` — bootstrap.
- `.../Startup.cs:26` — startup wiring.
- Controllers:
  - `.../Controllers/StorageController.cs:40` `GET /api/storage/{jobId}`.
  - `:93` `GET /api/storage/{jobId}/joboutputcontainer`.
  - `.../Controllers/JobsController.cs:24` `GET /api/jobs/detail/{jobId}`.
  - `:37` `GET /api/jobs/PortalDetails`.
- Hosted services: `InputCacheCleanupService`, `InputContainerCleanupService` (`Startup.cs:39-40`).

## Key Dependencies

- Azure Storage SDK (Blob/Table/Queue).
- `Rainier.ServiceToolkit` — JWT, health.
- `OriginConstants` — container/queue/table names.
- `SasFactory` — builds SAS URIs.
- `IUtilInfo`, `IPortalUriProvider`.

## Related Features

- [Run Projections](run-projections.md)
- [Job Management](job-management.md)

## Change Log

- 2026-04-21: Seeded.
