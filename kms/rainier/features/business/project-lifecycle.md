# Project Lifecycle (fork / import / archive)

- **Category:** business
- **Status:** active
- **Owners:** authoring-and-governance

## Summary

Full lifecycle operations on Integrate projects — forking an existing project, importing from files/IDMX, archiving, renaming, deleting, replacing, copying, and moving.

## Behavior

- **Fork** a project from any commit.
- **Import** via IDMX (Integrate Delivery Model Exchange), raw files, or full zip.
- **Archive** — retire a project without deleting it, then later re-hydrate.
- **Rename / Copy-Rename / Move** — project metadata changes.
- **Replace Model** — swap the current branch/project with an imported model.
- **Apply Patch** — apply a packaged change set.
- **Download** — project zip or tree zip for inspection/handoff.

## Entry Points

- Modals: `monorepo/desktop/ui/packages/project-manager/src/components/project-manager/modal-tool/modals/`:
  - `fork-*`, `import-project-modal/`, `import-idmx-modal`, `import-model-point-file-modal/`, `export-idmx-modal`, `apply-patch-*`, `replace-model-*`, `copy-rename-*`, `move-*`, `rename-project-modal.tsx`, `delete-project-modal.tsx`, `archive-project-modal.tsx`.
- Backend:
  - `src/server/Rainier.ProjectManager/Rainier.ProjectManager.Server/Controllers/ProjectsImportController.cs:20` — `ImportFiles`, `ImportForkedFiles`, `ValidateImportFiles`.
  - `.../Controllers/ProjectsArchiveController.cs:20` — `Archive`, `DeleteProjectFolder`.
  - `.../Controllers/ProjectReplaceController.cs:19` — branch/project replace.
  - `.../Controllers/ProjectZipDownloadController.cs:19` — `activeprojectdownload`, tree download.
- Common buttons: `packages/project-manager/src/components/project-manager/common/project-action-buttons/`.

## Key Dependencies

- `ImportProjectCommand`, `ApplyPatchCommand`, `DownloadDatabaseCommand` (MediatR).
- `Rainier.Core/Domain/Projects/LocalProjectContext*` for working-folder manipulation.
- `IIntegrateWorkspace` / `WorkingFolderBase`.

## Related Features

- [Project Authoring](project-authoring.md)
- [Sync, AutoMerge, SyncMerge](sync-and-merge.md)
- [Model Point File Import](model-point-file-import.md)

## Change Log

- 2026-04-21: Seeded.
