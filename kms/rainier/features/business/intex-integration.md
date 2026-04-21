# Intex Integration

- **Category:** business
- **Status:** active
- **Owners:** projection-execution

## Summary

Integrates **Intex** (a third-party structured-finance deal engine) into MG-ALFA projection execution. Used for modeling structured securities cash flows as part of actuarial projections.

## Behavior

- During a projection run, if Intex deals are referenced, the Intex adapter is invoked alongside the core calc engine.
- Deal files are located, loaded, and cash flows incorporated into the model's outputs.
- There is APL-side support (`UI/AplSource/IntexDeal/`) and a .NET integration layer.

## Entry Points

- Library: `monorepo/projection-execution/src/MgAlfa.Intex/`.
- Tests: `.../MgAlfa.Intex.Tests/`.
- APL source: `monorepo/mg-alfa/UI/AplSource/IntexDeal/`.
- APL interface txn: `monorepo/mg-alfa/NUI/NUI/Milliman.MGAlfa.AplInterface/TxnIntexIntegration.cs`.
- Backup helper: `monorepo/projection-execution/src/MgAlfa.ProjectionExecution/MgAlfa.ProjectionExecution.RegressionTest/IntexAwareBackup.cs` (makes regression runs Intex-aware).

## Key Dependencies

- External Intex runtime / deal files (not in repo).
- [APL Interface / Transactions](../dev/apl-interface.md).
- [Run Projections](run-projections.md).

## Related Features

- [Run Projections](run-projections.md)
- [Projection Execution Regression Suite](../dev/projection-regression-suite.md)

## Change Log

- 2026-04-21: Seeded.
