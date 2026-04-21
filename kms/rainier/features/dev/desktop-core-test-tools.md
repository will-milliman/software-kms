# DesktopCore Test Tools

- **Category:** dev
- **Status:** active
- **Owners:** desktop-core

## Summary

`monorepo/desktop-core/` hosts shared test artifacts, fixtures, and helpers reused across UI, MG-ALFA, MMD, and Projection Execution test suites.

## Behavior

- Provides sample GDF files, ETL inputs, and synthetic model points used by multiple projects.
- Exposes NUnit/xUnit base classes and Playwright page objects.
- Published as NuGet/npm packages to the internal feeds when cross-repo consumers need them.

## Entry Points

- `monorepo/desktop-core/`.

## Key Dependencies

- [GDF File Format Library](gdf-file-format.md).

## Related Features

- [Projection Execution Regression Suite](projection-regression-suite.md)
- [Playwright E2E Orchestration](playwright-e2e-orchestration.md)

## Change Log

- 2026-04-21: Seeded.
