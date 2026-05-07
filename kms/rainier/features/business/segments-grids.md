# Segments Grids

- **Category:** business
- **Status:** active
- **Owners:** authoring-and-governance

## Summary

Authoring UI and Project Manager APIs for segment data grids, including liability segments, asset segments, and reinvestment inputs on the model Segments page.

## Behavior

- Segment tabs are feature-flagged and exposed from the Project Manager authoring experience.
- Server command handlers provide grid row data, column definitions, and save commands.
- UI endpoints use AG Grid query APIs for the segment-specific tables.
- System tests cover each segment endpoint and startup path.

## Entry Points

- `src/server/Rainier.ProjectManager/Rainier.ProjectManager.Server/Controllers/AssetSegmentsController.cs`.
- `src/server/Rainier.ProjectManager/Rainier.ProjectManager.Server/Controllers/LiabilitySegmentsController.cs`.
- `src/server/Rainier.ProjectManager/Rainier.ProjectManager.Server/Controllers/ReinvestmentInputsController.cs`.
- `monorepo/desktop/ui/packages/project-manager/src/components/project-manager/authoring.view/model/liability-segments/`.
- `monorepo/desktop/ui/packages/store/src/slices/api/project-manager/endpoints/*-segments/*-ag-grid-query-api.ts`.

## Key Dependencies

- [Project Authoring](./project-authoring.md)
- [Feature Toggles](./feature-toggles.md)

## Related Features

- [Governance](./governance.md)
- [Projection Execution Regression Suite](../dev/projection-regression-suite.md)

## Change Log

- 2026-05-07: PR #9666 Add Liability Segments Query Grid (server + UI + feature flag) — introduced liability segment server endpoints, UI tab, store query API, and system tests.
- 2026-05-07: PR #9680 Add Asset Segments tab to Segments page — added asset segment grid UI/API wiring and tests.
- 2026-05-07: PR #9712 Add reinvestments tab to the segments grid — added reinvestment inputs grid alongside asset and liability segments.
