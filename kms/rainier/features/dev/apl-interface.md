# APL Interface / Transactions

- **Category:** dev
- **Status:** active
- **Owners:** mg-alfa / calc engine

## Summary

The transactional RPC layer between C# hosts (MG-ALFA Classic, Projection Execution) and the Dyalog APL Calc Engine. Marshals requests into APL workspace calls and returns results as structured .NET objects.

## Behavior

- A transaction frame wraps a unit of APL work with state save/restore so failures can be rolled back.
- Supports synchronous calls for editor operations and streamed progress for projection runs.
- Hides Dyalog specifics from upstream C# code.

## Entry Points

- `monorepo/mg-alfa/src/` — search `AplTransaction`, `IAplHost`.

## Key Dependencies

- Dyalog APL runtime (bundled per platform).

## Related Features

- [MG-ALFA Classic Editors](../business/mg-alfa-classic-editors.md)
- [UDF Code Generation](udf-code-generation.md)

## Change Log

- 2026-04-21: Seeded.
