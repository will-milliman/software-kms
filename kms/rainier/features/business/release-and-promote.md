# Release & Promote (IFP/IDM)

- **Category:** business
- **Status:** active
- **Owners:** authoring-and-governance

## Summary

Publish a commit and promote a model to downstream Milliman platforms — IFP (Integrate Forecasting Platform) and IDM (Integrated Delivery Model). Gated by dedicated permissions.

## Behavior

- From the project header, a user with the right permissions can **Promote** the current branch/commit.
- The system records a `Promote` record, surfaces promotion status, and triggers downstream pipelines.
- **Publish** a commit makes it official within the Integrate store; **Revert** undoes a publication.
- Promotion history is visible in the promotions grid.
- Permissions required: `PromoteModelToIfp` (and `ViewPromote` to see the grid).

## Entry Points

- UI:
  - `monorepo/desktop/ui/packages/project-manager/src/components/project-manager/authoring.view/promote/`:
    - `promotion-status/`, `promotions-grid/`, `promotion-table-row-builder/`.
  - Modals:
    - `modal-tool/modals/promote-project-modal.tsx`, `promotion-status-modal.tsx`, `publish-modal/`, `share-modal/`.
- Backend:
  - `src/server/Rainier.ProjectManager/Rainier.ProjectManager.Server/Controllers/CommitPublishController.cs:19-56` — `CommitPublish`, `ProjectCommitPublish`, `RevertToBranchCommit`, `RevertToProjectCommit`.
  - Policies: `Startup.cs:73-74` — `PromoteModelToIfp`.
- Release drafting: `PromoteEnhancements` feature toggle (`app-toggles.ts`).

## Key Dependencies

- MediatR commands in Rainier.ProjectManager.
- Polly-wrapped HTTP client to cloud-side promotion endpoints.
- ACL check via `IPermissionHandler`.

## Related Features

- [Branches & Commits](branches-and-commits.md)
- [ACL / Permissions](acl-permissions.md)
- [Release Notes](release-notes.md)

## Change Log

- 2026-04-21: Seeded.
