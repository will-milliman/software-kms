# Model Point File Import

- **Category:** business
- **Status:** active
- **Owners:** authoring-and-governance

## Summary

Import a seriatim model-point file (policies/contracts) into a model through a governed endpoint — so the action is permission-checked and auditable.

## Behavior

- User picks a file via the import modal.
- Governance validates the user has `Authoring.Edit` permission.
- The file is placed into the workspace; the model reference is updated.

## Entry Points

- UI modal: `monorepo/desktop/ui/packages/project-manager/src/components/project-manager/modal-tool/modals/import-model-point-file-modal/`.
- Backend: `src/server/Rainier.Governance/Rainier.Governance.Server/Controllers/GovernanceController.cs:47` — `POST /api/governance/ImportModelPointFile` (attribute `[RequiresPermission(Permissions.Authoring.Edit)]`).
- Command: `ImportModelPointFileCommand` (MediatR).

## Key Dependencies

- [Governance](governance.md).
- `Rainier.Server.Core/Security/RequiresPermission.cs`.

## Related Features

- [Governance](governance.md)
- [Project Authoring](project-authoring.md)

## Change Log

- 2026-04-21: Seeded.
