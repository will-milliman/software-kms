# UDF Code Generation

- **Category:** dev
- **Status:** active
- **Owners:** mg-alfa / calc engine

## Summary

User-defined formulas (UDFs) written in MG-ALFA's formula language are compiled to native/managed code for fast execution during projection runs, rather than being interpreted at each time step.

## Behavior

- Formula source is parsed, validated ([Formula DB & Validation](../business/formula-db.md)), and lowered to an IR.
- The code generator emits C (or managed IL, depending on path) that's compiled and loaded into the calc engine process.
- Cached by formula hash so repeated runs avoid recompilation.

## Entry Points

- `monorepo/mg-alfa/src/` — search for `UdfCompiler`, `FormulaCodeGenerator`.

## Key Dependencies

- [APL Interface / Transactions](apl-interface.md) for runtime glue.
- [Formula DB & Validation](../business/formula-db.md) for the parser.

## Related Features

- [Calc Engine Bundles](calc-engine-bundles.md)
- [Projection Execution Regression Suite](projection-regression-suite.md)

## Change Log

- 2026-04-21: Seeded.
