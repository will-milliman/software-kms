# Formula DB & Validation

- **Category:** business
- **Status:** active
- **Owners:** authoring-and-governance

## Summary

Authoring, parsing, validation, and refactoring of MG-ALFA formula text (the actuarial DSL stored in `.Adb2` files). Includes safe variable renaming across the formula database.

## Behavior

- Users edit formulas in a dedicated editor surface with syntax highlighting and error feedback.
- On save, formulas are parsed and business-rule-validated.
- Rename-variable refactors rewrite all references across the formula database transactionally.

## Entry Points

- Library: `monorepo/mg-alfa/NUI/NUI/Milliman.MGAlfa.FormulaDb/`:
  - `Formula.cs` (23 KB) — core model.
  - `FormulaCollection.cs`, `FormulaView.cs` (14 KB).
  - `FormulaTextBuilder.cs`, `FormulaTextRewriting.cs` — text construction + refactor.
  - `RenameVariableIntermediateData.cs` — rename-variable support.
  - `Validation.cs` (15 KB), `BusinessRules.cs` — validation.
  - Subfolders: `AdxSupport/`, `Import/`, `Operations/`.
- UI controls: `monorepo/mg-alfa/NUI/NUI/Milliman.MGAlfa.UI.Controls.FormulaDb/`.
- Editor launcher: `monorepo/mg-alfa/NUI/editors/src/FormulaDatabaseLauncher.cs`.
- Build pipeline: `monorepo/mg-alfa/.pipelines/Milliman.MGAlfa.FormulaDb.yaml`.

## Key Dependencies

- [APL Interface / Transactions](../dev/apl-interface.md) — formulas are committed through APL.
- [GDF File Format Library](../dev/gdf-file-format.md) — Adb2 persistence.

## Related Features

- [MG-ALFA Classic Editors](mg-alfa-classic-editors.md)

## Change Log

- 2026-04-21: Seeded.
