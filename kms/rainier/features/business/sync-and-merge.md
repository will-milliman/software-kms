# Sync, AutoMerge, SyncMerge

- **Category:** business
- **Status:** active
- **Owners:** authoring-and-governance

## Summary

Three flavors of reconciling local and remote project state. **Sync** pulls the latest cloud state, **AutoMerge** applies an automatic three-way merge when safe, and **SyncMerge** opens the Developer Desktop conflict-resolution UI when user input is required.

## Behavior

- After working on a local branch, users trigger Sync to bring in remote changes.
- If no conflicts: AutoMerge completes silently, result is committed locally.
- If conflicts detected: SyncMerge launches — the ProjectManager delegates to a DeveloperDesktop.exe instance which surfaces node-level diffs and collects user resolutions.
- The user can Accept, Cancel, or Review the merge outcome before it commits.

## Entry Points

- Backend:
  - `src/server/Rainier.ProjectManager/Rainier.ProjectManager.Server/Controllers/ProjectsController.cs:150` — Sync.
  - `:166` — AutoMerge.
  - `:184, :206` — SyncMerge variants.
  - `:222, :230, :239` — Accept / Cancel / Review.
  - Commands: `SyncAutoMergeCommand`, `SyncMergeCommand`.
- UI modals: `monorepo/desktop/ui/packages/project-manager/src/components/project-manager/modal-tool/modals/sync-auto-merge-project-modal.tsx`, `merge-project-modal.tsx`, `sync-branch-modal/`.
- Debug override: `src/server/Rainier.ProjectManager/Rainier.ProjectManager.Server/Infrastructure/Configuration/CloudConfig.cs` (`DebugSyncMergeClient` flag + `DEBUG` pragma) — documented in root `README.md:376`.

## Key Dependencies

- Polly-wrapped HTTP client to MMD cloud (`Startup.cs:199-215`).
- DeveloperDesktop build artifact (path `C:\Users\...\AppData\Local\Programs\integrate\resources\DeveloperDesktop\####.##.##\`).
- `Rainier.Server.Core/Domain/ProjectChanges/EditSession.cs`, `GdfFileHasher.cs`.

## Related Features

- [Conflict Resolution](conflict-resolution.md)
- [Branches & Commits](branches-and-commits.md)
- [MMD Collaborative Development](mmd-collaborative-development.md)

## Change Log

- 2026-04-21: Seeded.
