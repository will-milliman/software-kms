# Excel Table Import/Export

- **Category:** business
- **Status:** active
- **Owners:** authoring-and-governance

## Summary

Round-trip MG-ALFA table files (`.Atb2`) to and from Excel workbooks via OpenXML. Supports large "IJ" (index-joined) tables and metadata sheets.

## Behavior

- Users export an Atb2 table to an Excel workbook for bulk editing (a common actuarial workflow).
- After editing, import the workbook back — the converter reconstructs the Atb2 binary.
- Metadata sheets describe table properties; worksheet names map to table names.

## Entry Points

- `monorepo/mg-alfa/NUI/Managed/Milliman.MGAlfa.Tables.Excel/`:
  - `ExcelToAtb2FileConverter.cs` — import converter.
  - `NewExcelExporter.cs` (10 KB) — export path.
  - `TablePropertiesParser.cs` — metadata sheet parsing.
  - `LargeIJTableParser.cs`, `FlexIJHeaders.cs` — large-table support.
  - `ExcelColumnHeadings.cs`, `ExcelTable.cs`, `InMemoryExcelTable.cs`, `OnDiskExcelTable.cs`, `InMemoryExcelTableService.cs` — table abstractions.
  - `TableSheetNameMap.cs` — worksheet ↔ table name mapping.
- Tests: `monorepo/mg-alfa/NUI/Milliman.MGAlfa.Tables.Excel.Tests/`.
- Build pipeline: `monorepo/mg-alfa/.pipelines/Milliman.MGAlfa.Tables.Excel.yaml`.

## Key Dependencies

- OpenXML SDK (Excel workbook I/O).
- [GDF File Format Library](../dev/gdf-file-format.md) — Atb2 is GDF-encoded.

## Related Features

- [MG-ALFA Classic Editors](mg-alfa-classic-editors.md)

## Change Log

- 2026-04-21: Seeded.
