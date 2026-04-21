# MG-ALFA Classic Editors

- **Category:** business
- **Status:** active
- **Owners:** authoring-and-governance (maintainers)

## Summary

The "Classic" suite of desktop editors — Database (Adb2), Input/Model (Ain2), Table (Atb2), Formula DB, and Inforce — originally shipped as a stand-alone MG-ALFA system. These are WinForms/WPF applications that interoperate with a Dyalog APL workspace for calculations and file mutation.

## Behavior

- Launched from Integrate (via ProjectManager) or as stand-alone executables.
- Each editor provides rich, domain-specific UI: tree navigation of model structure, cell grids, formula editors, table import/export.
- All persistent changes are mediated through APL transactions (for calc-engine consistency) and saved in GDF format.

## Entry Points

- WPF main: `monorepo/mg-alfa/NUI/NUX/Source/MgAlfa.Executable/App.xaml.cs:63`.
- WinForms host: `monorepo/mg-alfa/NUI/NUI/Milliman.MGAlfa.UI/HostWindow.cs`.
- Editor launchers: `monorepo/mg-alfa/NUI/editors/src/Program.cs:15` → `App.cs` → `EditorRunner.cs`:
  - `ModelEditorLauncher.cs` (Ain2 Database/Model)
  - `InputEditorLauncher.cs` (Input)
  - `TableEditorLauncher.cs` (Table)
  - `FormulaDatabaseLauncher.cs` (Formula DB)
- Editor UI:
  - `monorepo/mg-alfa/NUI/NUI/Milliman.MGAlfa.UI/DatabaseEditorDialogs/`
  - `monorepo/mg-alfa/NUI/NUI/Milliman.MGAlfa.UI/Input/`, `Tables/`, `FormulaDb/`
  - `monorepo/mg-alfa/NUI/NUI/Milliman.MGAlfa.UI.Controls.Input/`, `Controls.Tables/`, `Controls.FormulaDb/`, `Controls.Inforce/`, `Controls.Grids/`.
- Domain models:
  - `monorepo/mg-alfa/NUI/Managed/Milliman.MGAlfa.Input/`, `Milliman.MGAlfa.Input.Excel/`, `Milliman.MGAlfa.Tables/`, `Milliman.MGAlfa.Tables.Excel/`, `Milliman.MGAlfa.Atb2.TextImport/`.

## Key Dependencies

- [APL Interface / Transactions](../dev/apl-interface.md) — underlying RPC layer.
- [GDF File Format Library](../dev/gdf-file-format.md).
- Dyalog APL runtime (from `DevTools/Dyalog/Dyalog101.7z`).
- VSTO-style Excel integration (OpenXML).

## Related Features

- [Formula DB & Validation](formula-db.md)
- [Excel Table Import/Export](excel-tables.md)
- [Project Authoring](project-authoring.md)

## Change Log

- 2026-04-21: Seeded.
