# Dependabot Grouped Updates

- **Category:** dev
- **Status:** active
- **Owners:** @milliman-lts/platform-engineering

## Summary

`.github/dependabot.yml` configures **five NuGet ecosystems** (one per service: audit-log, auth-server, reports, tenant-admin — plus one for each as a separate directory) and **one npm ecosystem** (ClientApp). Each ecosystem uses **version-pattern groups** to minimise PR churn.

## Behavior

- NuGet: daily @ 02:00 Europe/London, PR limit 10, ignores `FluentAssertions` globally. Three groups per service: `<svc>-ms-extensions-configuration` (Microsoft.Extensions.Configuration.*), `<svc>-dev-tooling` (Roslyn analyzers, coverlet, StyleCop, xUnit family), `<svc>-everything-else` (catch-all excluding the other two).
- npm (ClientApp): weekly Monday @ 02:00, ignores semver-patch, `@integrate-core-ui/*`, `prettier`, `@trivago/prettier-plugin-sort-imports`, `husky`, `lint-staged`, `wait-on`. Groups: `react`, `tanstack-router`, `carbon`, `testing`, `eslint`, `swc`, `powerbi`, `babel`, `styled-components`, `build-tools`.

## Entry Points

- `.github/dependabot.yml:1-206`.

## Key Dependencies

- GitHub Dependabot (version 2 schema).

## Related Features

- [Azure DevOps Pipelines](./azure-devops-pipelines.md)

## Change Log

- 2026-04-21: Seeded.
