# Project Authoring

- **Category:** business
- **Status:** active
- **Owners:** authoring-and-governance (CODEOWNERS default)

## Summary

The core authoring experience in Integrate: creating, organizing, and editing projects that contain MG-ALFA models, ETLs, scenarios, factor files, and configuration. The UI is the main surface of the Integrate web renderer.

## Behavior

- Users open the authoring view and see a tree/explorer of projects, branches, and files.
- They can open model editors (Database, Input, Table, Formula DB), scenario files, factor files, model points, assumption tables, deployments, and projection content.
- Changes are tracked via edit sessions on the server; progress flows back via SignalR.
- The header exposes action buttons for share, archive, discard changes, etc.
- Changes in the tree are reflected live; write-lock prevents concurrent edits.

## Entry Points

- `monorepo/desktop/ui/packages/project-manager/src/components/project-manager/authoring.view/authoring-view.tsx:23` — root authoring view.
- `monorepo/desktop/ui/packages/project-manager/src/components/project-manager/authoring-explorer/` — file/branch explorer.
- `monorepo/desktop/ui/packages/project-manager/src/components/project-manager/authoring.view/model/` — model authoring subtree (`actuarial-inputs/`, `assumption-tables/`, `configuration/`, `deployments/`, `economic-scenarios/`, `factor-files/`, `model-points/`, `projection-content-area/`, `scenario-files/`).
- `src/server/Rainier.ProjectManager/Rainier.ProjectManager.Server/Controllers/ProjectsController.cs:29` — REST backend (Open/Add/etc.).
- `src/server/Rainier.ProjectManager/Rainier.ProjectManager.Server/Controllers/FileEditorsController.cs:19` — opened-editor state.
- Per-file-type controllers: `TableFilesController.cs`, `ScenarioFilesController.cs`, `FactorFilesController.cs`, `ReferenceFilesController.cs`, `DatabaseFilesController.cs`, `Ain2FilesController.cs`.
- `src/server/Rainier.ProjectManager/Rainier.ProjectManager.Server/Controllers/ProjectionKeyDefinitionsController.cs:20` — projection key definitions.

## Key Dependencies

- `@repo/project-manager`, `@repo/store` (RTK Query), `@repo/components`, `@repo/signalr-service`.
- MediatR command handlers server-side.
- `Rainier.Core` domain types (`LocalProjectContext`, etc.).
- `@repo/project-write-lock` to enforce single-writer semantics per project.

## Related Features

- [Branches & Commits](branches-and-commits.md)
- [Sync, AutoMerge, SyncMerge](sync-and-merge.md)
- [Release & Promote (IFP/IDM)](release-and-promote.md)
- [MG-ALFA Classic Editors](mg-alfa-classic-editors.md)
- [ETL Authoring](etl-authoring.md)
- [Governance](governance.md)

## Change Log

- 2026-04-21: Seeded.
