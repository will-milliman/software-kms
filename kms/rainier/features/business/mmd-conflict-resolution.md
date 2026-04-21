# MMD Conflict Resolution

- **Category:** business
- **Status:** active
- **Owners:** authoring-and-governance / calculation-and-execution

## Summary

Three-way merge UI inside MMD Developer Desktop for resolving GDF-node-level conflicts that arise when multiple developers modify the same shared model. Uses the same core engine as Integrate's SyncMerge flow.

## Behavior

- On sync, if conflicts are detected, Developer Desktop opens the conflict resolution window.
- Each conflicting GDF node is presented with local, remote, and base values.
- User picks a resolution per node (or bulk).
- The merged result is written back through the standard commit flow.

## Entry Points

- Desktop integration: `monorepo/model-development/src/DiffMergeClient/DeveloperDesktop/ConflictResolutionInstaller.cs` — DI wiring.
- Engine: `monorepo/model-development/src/DiffMergeClient/ConflictResolution/ConflictResolution.Core/`.
- Tests: `.../ConflictResolution/Tests/`.

## Key Dependencies

- [Conflict Resolution Engine](../dev/conflict-resolution-engine.md) — the library.
- [GDF File Format Library](../dev/gdf-file-format.md) — node-level diff primitives (`Gdf/Diff/`).

## Related Features

- [Conflict Resolution](conflict-resolution.md) — Integrate-side counterpart.
- [MMD Collaborative Development](mmd-collaborative-development.md)
- [Sync, AutoMerge, SyncMerge](sync-and-merge.md)

## Change Log

- 2026-04-21: Seeded.
