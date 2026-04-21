# Power BI Embedded NuGet Library

- **Category:** dev
- **Status:** active
- **Owners:** @milliman-lts/platform-engineering

## Summary

`Integrate.SaaS.Reports` is a **NuGet-packable class library** (`reports/src/Integrate.SaaS.Reports/`) that wraps the Power BI Embedded REST SDK behind an `IEmbeddedReportService` interface. Tenant-admin depends on it; other Integrate services can too.

## Behavior

- `AddEmbeddedReports<TBuilder>(IServiceCollection, IConfiguration)` binds `ReportOptions` and registers singletons `BearerTokenFactory`, `PowerBIClientFactory`, `ReportClientFactory` plus a transient `IEmbeddedReportService`.
- `AddEmbeddedReportsInstrumentation(TracerProviderBuilder)` adds `SemanticConventions.InstrumentationSource` to OpenTelemetry.
- `ListReportsAsync` enumerates workspaces/reports the caller's `customer` claim can see.
- `GetEmbeddedReportAsync` mints an embed token for a specific report.
- Published by `reports/.pipelines/reports.build.yaml` (dotnet restore/build/test/pack/push) to the internal `milliman` AzDO feed.

## Entry Points

- `reports/src/Integrate.SaaS.Reports/ServiceCollectionExtensions.cs:9` — `AddEmbeddedReports<TBuilder>`.
- `reports/src/Integrate.SaaS.Reports/ServiceCollectionExtensions.cs:21` — `AddEmbeddedReportsInstrumentation`.
- `reports/src/Integrate.SaaS.Reports/IEmbeddedReportService.cs:5-36` — public API.
- `reports/src/Integrate.SaaS.Reports/EmbeddedReportService.cs:11` — implementation.
- `reports/.pipelines/reports.build.yaml:1-64` — CI.
- `reports/src/Integrate.SaaS.Reports/readme.md` — packaged NuGet readme.

## Key Dependencies

- `Microsoft.PowerBI.Api` 4.22.0.
- `Microsoft.Identity.Client` 4.73.0 (MSAL).
- `OpenTelemetry.Api` 1.12.0.
- `System.Linq.Async` 6.0.3.

## Related Features

- [Embedded Power BI Reports](../business/embedded-powerbi-reports.md)

## Change Log

- 2026-04-21: Seeded.
