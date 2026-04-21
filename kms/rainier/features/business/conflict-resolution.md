# Conflict Resolution

- **Category:** business
- **Status:** active
- **Owners:** authoring-and-governance (UI), calculation-and-execution (engine)

## Summary

The user-facing resolution UI triggered by SyncMerge. Handles three-way merges at the level of GDF entity nodes, letting users pick local, remote, or manual-merge outcomes per conflicting node.

## Behavior

- After a SyncMerge detects conflicts, Integrate launches DeveloperDesktop in "conflict resolution" mode.
- The UI shows conflicting nodes side-by-side (local vs incoming vs base).
- For each conflict, the user selects a resolution.
- The result is returned to ProjectManager which commits a merge commit.
- Includes ETL-specific conflict-resolution upgrade path gated by the `UseEtlMergeConflictResolutionUpgrade` feature toggle.

## Entry Points

- UI (in-app):
  - `monorepo/desktop/ui/packages/project-manager/src/components/project-manager/modal-tool/modals/sync-branch-modal/`.
  - `packages/project-manager/src/components/project-manager/modal-tool/modals/etl-validation-modal-notification/`.
  - Store selectors `select-is-resolving-merge`, hook `use-is-resolving-merge-conflict` in `@repo/store`.
- DeveloperDesktop engine: `monorepo/model-development/src/DiffMergeClient/ConflictResolution/ConflictResolution.Core/`.
- DI: `monorepo/model-development/src/DiffMergeClient/DeveloperDesktop/ConflictResolutionInstaller.cs`.
- Policy: `Rainier.ProjectManager.Server/Startup.cs:73` — `ConflictResolution` auth policy.

## Key Dependencies

- [Conflict Resolution Engine](../dev/conflict-resolution-engine.md) (dev counterpart).
- `ConflictResolution.Autofac` DI package.
- GDF node-level diff (`monorepo/desktop-core/src/Milliman.MGAlfa.Core/Gdf/Diff/`).

## Related Features

- [Sync, AutoMerge, SyncMerge](sync-and-merge.md)
- [MMD Conflict Resolution](mmd-conflict-resolution.md)
- [MMD Collaborative Development](mmd-collaborative-development.md)

## Change Log

- 2026-04-21: Seeded.
