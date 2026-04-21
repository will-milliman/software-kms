# Job Management

- **Category:** business
- **Status:** active
- **Owners:** projection-execution

## Summary

View job history, drill into job details, pick a folder for results, and perform bulk/batched downloads of outputs across jobs and projections.

## Behavior

- Job History view lists recent jobs with status, duration, user, and results.
- Drilling in shows projection-level detail.
- Folder picker lets users stage output destinations.
- Batched download endpoint supports bulk export of multiple job results.
- Running cloud jobs and local jobs both appear in one unified list.

## Entry Points

- UI: `monorepo/desktop/ui/packages/project-manager/src/components/components/job-manager/` — `job-details.view/`, `job-history.view/`, `job-folder-picker/`, `job-helpers.ts`.
- Backend (RunManager):
  - `src/server/Rainier.RunManager/Rainier.RunManager.Server/Controllers/JobsController.cs:52` — `GET /api/jobs/RunningCloudJobs`.
  - `:59, :66, :74, :84` — Download endpoints (single, all, batched, all projection).
  - `:181, :190` — History, Delete.
- Hosted services: `JobReattachService`, `JobCleanUpProcess`, `DiskSpaceStatsService` (`Startup.cs:58-60`).

## Key Dependencies

- SignalR for live status updates.
- Local job store + cloud job proxy.

## Related Features

- [Run Projections](run-projections.md)
- [Cloud Run Storage (Origin)](cloud-run-storage.md)

## Change Log

- 2026-04-21: Seeded.
