# Conversion Tools

- **Category:** dev
- **Status:** active
- **Owners:** calculation-and-execution

## Summary

Shared conversion tooling for Excel, inforce, and scenario inputs used by Projection Execution and Calc Engine SDK integration. This replaces the older Excel-only conversion surface with a generalized IntegrateConvert command model and SDK conversion APIs.

## Behavior

- `IntegrateConvert` exposes command handlers for Excel, inforce, and scenario conversion.
- Conversion result messages are pruned so users see actionable failures rather than raw stack traces.
- SDK conversion APIs/protobuf models carry conversion parameters, run type, distribution type, and conversion result metadata.
- E2E, nightly, and unit tests cover Excel, inforce, scenario, and logging paths.

## Entry Points

- `monorepo/projection-execution/src/Conversions/IntegrateConvert/` — command handlers and conversion host.
- `src/server/Rainier.RunManager/Scripting/Rainier.IntegrateScripting/Api/Conversion/` — SDK-facing conversion API.
- `src/server/Rainier.RunManager/Scripting/Rainier.IntegrateScripting/PythonInterop/src/calc_engine_sdk/conversions/`.
- `src/server/Rainier.RunManager/Scripting/Rainier.IntegrateScripting/PythonInterop/protos/calc_engine_sdk/domain/generated/`.

## Key Dependencies

- [Run Projections](../business/run-projections.md)
- [SDK AI Agent](./sdk-ai-agent.md)

## Related Features

- [Projection Execution Regression Suite](./projection-regression-suite.md)
- [User Docs Build](./user-docs-build.md)

## Change Log

- 2026-04-22: PR #9615 Prune Stack Traces From Conversion Messages — conversion handlers now emit pruned user-facing messages instead of full stack traces.
- 2026-05-07: PR #9616 Remove Legacy Scenario Conversion Api — removed the older scenario conversion API surface in favor of the generalized conversion model.
- 2026-05-07: PR #9637 SDK release v0.4.x — expanded SDK conversion APIs, protobufs, tests, user docs, and release notes for Excel/inforce/scenario conversion.
- 2026-05-07: PR #9691 RunType and DistributionType mappings for protobuf — added protobuf/domain mappings for run type and distribution type.
