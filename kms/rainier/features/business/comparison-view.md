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
  - `monorepo/model-development/src/Server/WebSite/ReactApps/Shared/hooks/use-current-version-history-details.ts` — reads `commitId` from query params; treats both `null` and the literal string `"null"` as missing so first-commit-on-branch compare URLs (where MMD serializes `null` as the string) no longer hang on a perpetual spinner (PR #9610, AB#90935).

## Key Dependencies

- [GDF File Format Library](../dev/gdf-file-format.md) — node-level diff.

## Related Features

- [Audit Reports & Change Details](audit-and-change-details.md)
- [Conflict Resolution](conflict-resolution.md)

## Change Log

- 2026-04-21: Seeded.
- 2026-04-22: PR #9610 AB#90935 check for 'null' string serialization in compare page hook — fixes perpetual spinner when comparing the first commit on a branch (mmd API serializes a null commitId as the string "null").
