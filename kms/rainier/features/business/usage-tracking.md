# Usage Tracking

- **Category:** business
- **Status:** active
- **Owners:** authoring-and-governance

## Summary

A "Usage" tool surfaces where and how various resources are used within a project (which projections consume which tables/databases/scenarios, etc.).

## Behavior

- User opens the Usage tool; sees a tree or report of resource consumption.
- Useful for impact analysis before renames, deletions, or governance changes.

## Entry Points

- UI:
  - `monorepo/desktop/ui/packages/project-manager/src/components/project-manager/usage-tool/`.
  - `monorepo/desktop/ui/packages/project-manager/src/components/project-manager/usage.tool/` (legacy naming).
- Mount: `monorepo/desktop/ui/apps/web/src/app.tsx` — `UsageTool` exposed via toolbox.

## Key Dependencies

- `@repo/store` for backing data.

## Related Features

- [Project Authoring](project-authoring.md)

## Change Log

- 2026-04-21: Seeded.
