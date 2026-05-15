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
- 2026-05-11: PR #9762 AB#81600 Bump the nunit-non-major group with 2 updates — refreshed NUnit-related test dependencies and assertions in DesktopCore tests.
- 2026-05-12: PR #9780 AB#81600 Bump the testing-non-major group with 1 update — updated `Microsoft.NET.Test.Sdk` in DesktopCore tests from 18.0.1 to 18.5.1.
- 2026-05-12: PR #9782 Remove strictPropertyInitialization and suppressImplicitAnyIndexErrors from @repo/store — tightened `@repo/store` TypeScript compiler settings and fixed dynamic-indexing errors in store tests/helpers.
- 2026-05-15: PR #9794 Remove useUnknownInCatchVariables and strictNullChecks from @repo/store — tightened store/model null handling and catch-variable typing across store selectors, slices, hooks, middlewares, and test helpers.
- 2026-05-15: PR #9814 Remove @ts-strict-ignore directives from packages/project-manager — enabled strict TypeScript mode in `@repo/project-manager` after removing all strict-ignore directives.
- 2026-05-15: PR #9817 Remove strict TypeScript compiler overrides from project-manager — removed remaining strict compiler option overrides from `@repo/project-manager` and fixed resulting type errors.
