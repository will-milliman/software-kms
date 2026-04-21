# GDF File Format Library

- **Category:** dev
- **Status:** active
- **Owners:** mg-alfa / core

## Summary

`Milliman.MGAlfa.Core/Gdf/*` — the entity-node serializer and versioned upgrade pipeline for GDF (Generalized Data Format) documents that back MG-ALFA projects, databases, tables, and formulas.

## Behavior

- Reads/writes GDF documents as strongly-typed entity nodes.
- Upgrade steps are numbered classes applied in order when loading an older file; each migrates the in-memory tree.
- Used by virtually every component that persists model content (Desktop, MMD, ETL, Projection Execution).

## Entry Points

- `src/shared/Milliman.MGAlfa.Core/Gdf/` (approximate).
- Upgrade step classes alongside.

## Key Dependencies

- Internal.

## Related Features

- [Conflict Resolution Engine](conflict-resolution-engine.md)
- [APL Interface / Transactions](apl-interface.md)

## Change Log

- 2026-04-21: Seeded.
