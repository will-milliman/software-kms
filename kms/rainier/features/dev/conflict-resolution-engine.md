# Conflict Resolution Engine

- **Category:** dev
- **Status:** active
- **Owners:** mmd / core

## Summary

`ConflictResolution.Core` is a library that implements three-way merge at the GDF-entity-node level, producing structured conflict objects the Developer Desktop UI renders for user resolution.

## Behavior

- Inputs: base, ours, theirs versions of a GDF document.
- Walks entity nodes, detecting adds/removes/edits on each side, and either auto-merges or emits a conflict with both sides + base.
- Output drives both the MMD conflict-resolution UI and the SyncMerge flow in Integrate.

## Entry Points

- `src/shared/ConflictResolution.Core/` (approximate path).
- Consumers: MMD Developer Desktop and Integrate SyncMerge command handlers.

## Key Dependencies

- [GDF File Format Library](gdf-file-format.md).

## Related Features

- [Conflict Resolution](../business/conflict-resolution.md)
- [Conflict Resolution in MMD](../business/mmd-conflict-resolution.md)
- [Sync, AutoMerge, SyncMerge](../business/sync-and-merge.md)

## Change Log

- 2026-04-21: Seeded.
