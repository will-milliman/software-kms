# ETL Authoring

- **Category:** business
- **Status:** active
- **Owners:** authoring-and-governance

## Summary

Author ETL (Extract/Transform/Load) jobs as part of a project — used to pipe external data into model inputs (model points, scenarios, factor files) within Integrate.

## Behavior

- The authoring tree exposes ETL resources per project.
- Users design/edit ETLs via an IDM adapter frame embedded in the authoring view.
- Notifications surface progress and validation errors.
- ETLs participate in branching, merging (with dedicated conflict-resolution upgrade path), and releases.
- Gated by the `UseEtlAuthoring` feature toggle (backend) + equivalent renderer flag.

## Entry Points

- UI:
  - `monorepo/desktop/ui/packages/project-manager/src/components/project-manager/authoring.view/etl/`:
    - `etl.constants.ts`, `idm-adapter-frame/`, `document.notification/`, `workspace-progress.notification/`.
  - Route: `packages/project-manager/src/components/project-manager/authoring.view/authoring-view-routes/authoring.view.idm.adapter.item.tsx`.
  - Conflict flow: `etl-validation-modal-notification/` (renderer-side validation).
- Backend:
  - Governance endpoints in `Rainier.Governance.Server/Controllers/WorkspacesController.cs` handle workspace config used by ETLs.
  - Feature toggle `UseEtlAuthoring` in `src/server/Rainier.Core/FeatureToggles/FeatureToggleConfig.cs:10`.

## Key Dependencies

- IDM adapter iframe (external).
- `UseEtlMergeConflictResolutionUpgrade` feature toggle for the merge upgrade path.

## Related Features

- [Project Authoring](project-authoring.md)
- [Conflict Resolution](conflict-resolution.md)
- [Feature Toggles](feature-toggles.md)

## Change Log

- 2026-04-21: Seeded.
