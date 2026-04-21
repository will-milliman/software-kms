# Branches & Commits

- **Category:** business
- **Status:** active
- **Owners:** authoring-and-governance

## Summary

Version-control primitives on Integrate projects: create/rename/delete branches, create release branches, merge feature branches, browse commits, and checkout specific commits.

## Behavior

- Branch selector dropdown in the header lets users switch branches.
- Create a new branch (regular or release) from the current state or a selected commit.
- Rename a branch; merge a feature branch.
- Checkout a branch commit, a release commit, or a project commit (rollback).
- Commits can be published (promoted) or reverted.

## Entry Points

- UI:
  - `monorepo/desktop/ui/packages/project-manager/src/components/project-manager/authoring.view/branches/`:
    - `branch-selector/`, `branch-change-dropdown/`, `create-branch-button/`.
  - Modals in `modal-tool/modals/`: `create-branch-modal.tsx`, `rename-branch-modal.tsx`, `merge-feature-branch-modal.tsx`, `sync-branch-modal/`.
- Backend:
  - `src/server/Rainier.ProjectManager/Rainier.ProjectManager.Server/Controllers/CommitCheckoutController.cs:19` — checkout on branch/release/project commits.
  - `src/server/Rainier.ProjectManager/Rainier.ProjectManager.Server/Controllers/CommitPublishController.cs:19` — `CommitPublish`, `ProjectCommitPublish`, `RevertToBranchCommit`, `RevertToProjectCommit`.
  - `src/server/Rainier.ProjectManager/Rainier.ProjectManager.Server/Controllers/ProjectsController.cs:29` — branch-related project operations.

## Key Dependencies

- MediatR command handlers.
- `IProjectHashesWatcher`, `IProjectContextWatcher` for reactive updates.
- SignalR `/signalr/projectmanager` (`GeneralHub`) for broadcasting branch/commit events.

## Related Features

- [Sync, AutoMerge, SyncMerge](sync-and-merge.md)
- [Release & Promote (IFP/IDM)](release-and-promote.md)
- [Conflict Resolution](conflict-resolution.md)

## Change Log

- 2026-04-21: Seeded.
