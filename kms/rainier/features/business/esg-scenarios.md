# ESG Scenarios

- **Category:** business
- **Status:** active
- **Owners:** calculation-and-execution

## Summary

Risk-neutral **Economic Scenario Generator (ESG)** scenario files shipped alongside the product. Delivered as quarterly `.bin` files plus the native validator/add-in DLLs. Used in market-consistent valuations.

## Behavior

- `.bin` files named by quarter (`201209.bin` through `202603.bin`, ~10 KB each) represent quarterly ESG scenarios.
- Accompanied by `XLDLL_val_x64.dll`, `XLDLL_val_x64_2013.dll`, `msvcp100.dll`, `msvcr100.dll` — the Excel add-in runtime.
- Consumed by MG-ALFA projections that reference risk-neutral scenarios.

## Entry Points

- Data drop: `monorepo/risk-neutral-esg/` — `.bin` files + DLLs.
- No code — data-only subtree.

## Key Dependencies

- External ESG generator (owned by FRM / Will Babcock per top-level README).
- [Run Projections](run-projections.md).

## Related Features

- [Run Projections](run-projections.md)

## Change Log

- 2026-04-21: Seeded.
