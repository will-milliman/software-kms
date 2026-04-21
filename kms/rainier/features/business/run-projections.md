# Run Projections

- **Category:** business
- **Status:** active
- **Owners:** projection-execution, calculation-and-execution

## Summary

Start, monitor, cancel, and retrieve results of MG-ALFA projection jobs. Supports three execution modes — local in-process, grid (HPC via GridUtils), and cloud (Azure via Origin/SAS) — behind a single user experience.

## Behavior

- User selects projections in the authoring view, clicks **Run**, confirms configuration.
- RunManager schedules the job, launches Projection Execution with a `ProjectionExecutionConfiguration` (calc engine directory, model path, projections list).
- Status streams back over SignalR; job history records every run.
- Results can be downloaded per-run, batched, or per-projection.
- User can cancel a running job; list compatible calc engines.

## Entry Points

- UI:
  - `monorepo/desktop/ui/packages/project-manager/src/components/project-manager/runs-tool/` — run submission surface.
  - `packages/project-manager/src/components/project-manager/modal-tool/modals/run-projections-confirmation-modal/`.
  - `packages/project-manager/src/components/project-manager/authoring.view/model/projection-content-area/`.
  - Job UI: `packages/project-manager/src/components/components/job-manager/` (details, history, folder picker).
- Backend (RunManager):
  - `src/server/Rainier.RunManager/Rainier.RunManager.Server/Controllers/JobsController.cs:99` — `POST /api/jobs/Run/{id}` v1.
  - `:152` — `POST /api/jobs/Run/{id}` v2.
  - `:162, :172, :190` — Start / Cancel / Delete.
  - `:92, :181` — Status / History.
  - `:197, :204` — `CompatibleCalcEngineVersions`, `ComposeProjectionExecution`.
  - SignalR hub: `Infrastructure/Hubs/ProjectionExecutionStatusHub.cs:8` → `/signalr/runmanager`.
- Projection Execution:
  - `monorepo/projection-execution/src/MgAlfa.ProjectionExecution/MgAlfa.ProjectionExecution.ConsoleHost/` — console host exe.
  - `monorepo/projection-execution/src/MgAlfa.RunPreparation/` — pre-run prep.
  - `monorepo/projection-execution/src/MgAlfa.Runs.Execution/` — run loop + AHost integration.
- Grid targets: `monorepo/grid-utils-integration/*.targets`.

## Key Dependencies

- `StartProjectionsCommand`, `CancelJobCommand`, `DeleteJobsCommand` (MediatR).
- `Milliman.Desktop.CalcEngine` nuget (calc engine native binaries).
- GridUtils external executables (`NestedProjection.JobManager.exe`, `AhostSolver.exe`, `CellCycle.Merge.exe`).
- `Rainier.Origin` for cloud SAS URIs (see [Cloud Run Storage](cloud-run-storage.md)).
- `Milliman.LTS.Communication.Core` for cross-process messaging.

## Related Features

- [Job Management](job-management.md)
- [Cloud Run Storage (Origin)](cloud-run-storage.md)
- [Intex Integration](intex-integration.md)

## Change Log

- 2026-04-21: Seeded.
