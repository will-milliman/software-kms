# Governance

- **Category:** business
- **Status:** active
- **Owners:** authoring-and-governance

## Summary

A dedicated local service — `Rainier.Governance.Server` — that handles all governed edits to models: metadata changes, projection settings (bulk and per-projection), database changes, reference breaking, project notes, and calc-engine-version upgrades. All endpoints are permission-gated.

## Behavior

- The renderer calls Governance endpoints (rather than writing files directly) whenever the action affects model governance.
- Every mutation requires the `Authoring.Edit` permission (enforced by `RequiresPermission` attribute).
- Writes are dispatched as MediatR commands, giving a uniform pipeline for logging/validation.
- Progress is relayed via SignalR `/signalr/governance`.

## Behavior — endpoint summary

| Action | Endpoint |
|---|---|
| Edit metadata | `POST /api/governance/EditMetadata` |
| Import model point file | `POST /api/governance/ImportModelPointFile` |
| Bulk edit projection settings | `POST .../BulkEditProjectionSettings` |
| Edit projection settings (single) | `POST .../EditProjectionSettings` |
| Change database | `POST .../ChangeDatabase` |
| Break resource reference | `POST .../BreakResourceReference` |
| Read project notes | `GET .../ProjectNotes` |
| Write project notes | `POST .../ProjectNotes` |
| Upgrade calc engine version | `POST .../UpdateCalcEngineVersion` |

## Entry Points

- `src/server/Rainier.Governance/Rainier.Governance.Server/Program.cs:15` — host bootstrap.
- `.../Startup.cs:38` — startup wiring.
- `.../Controllers/GovernanceController.cs:19` — `[RequiresPermission(Permissions.Authoring.Edit)]` applied at class level.
  - `:34` EditMetadata, `:47` ImportModelPointFile, `:63` BulkEditProjectionSettings, `:80` EditProjectionSettings, `:97` ChangeDatabase, `:110` BreakResourceReference, `:122/:132` ProjectNotes, `:143` UpdateCalcEngineVersion.
- `.../Controllers/WorkspacesController.cs:16` — workspace templates (see [Workspace Templates](workspace-templates.md)).
- `.../Controllers/CalcEngineFeaturesController.cs:10` — `GET /api/calcenginefeatures/...`.
- SignalR: `GovernanceHub` → `/signalr/governance` (`Startup.cs:159`).

## Key Dependencies

- MediatR: `EditResourceMetadataCommand`, `ImportModelPointFileCommand`, `BulkEditProjectionSettingsCommand`, `ChangeDatabaseCommand`, `BreakResourceReferenceCommand`, `UpdateCalcEngineVersionCommand`.
- `Rainier.Core` domain types (`ModelFilePath`, `ResourceFileType`, `MetadataEntry`, `ReadOnlyResource`).
- `Rainier.Server.Core/Security/` — `Permissions.Authoring.Edit`.

## Related Features

- [Workspace Templates](workspace-templates.md)
- [Model Point File Import](model-point-file-import.md)
- [Project Authoring](project-authoring.md)

## Change Log

- 2026-04-21: Seeded.
