# Audit Reports & Change Details

- **Category:** business
- **Status:** active
- **Owners:** authoring-and-governance

## Summary

Surface per-project change history, audit-oriented reports, and detailed per-change views so users and compliance staff can review who changed what and when.

## Behavior

- Change Details view shows per-commit changes with file-level diffs.
- Audit Reports view aggregates changes in a reportable format.

## Entry Points

- UI:
  - `monorepo/desktop/ui/packages/project-manager/src/components/project-manager/change-details.view/`.
  - `monorepo/desktop/ui/packages/project-manager/src/components/project-manager/audit-reports/`.
- Backend:
  - Data flows from `Rainier.ProjectManager.Server` — relevant commands and query handlers under `src/server/Rainier.ProjectManager/`.

## Key Dependencies

- `@repo/store` — RTK Query slices.
- [Governance](governance.md) — for metadata/notes.

## Related Features

- [Branches & Commits](branches-and-commits.md)
- [Governance](governance.md)

## Change Log

- 2026-04-21: Seeded.
