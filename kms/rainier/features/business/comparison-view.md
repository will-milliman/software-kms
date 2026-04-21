# Comparison View

- **Category:** business
- **Status:** active
- **Owners:** authoring-and-governance

## Summary

Side-by-side comparison of two project/branch/commit states to visualize differences.

## Behavior

- User picks two sources to compare (e.g., current vs a past commit, or two branches).
- A comparison view renders the differences.

## Entry Points

- UI:
  - `monorepo/desktop/ui/packages/project-manager/src/components/project-manager/comparison/comparison-view.tsx`.

## Key Dependencies

- [GDF File Format Library](../dev/gdf-file-format.md) — node-level diff.

## Related Features

- [Audit Reports & Change Details](audit-and-change-details.md)
- [Conflict Resolution](conflict-resolution.md)

## Change Log

- 2026-04-21: Seeded.
