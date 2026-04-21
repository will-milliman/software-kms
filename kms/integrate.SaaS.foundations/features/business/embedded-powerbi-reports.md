# Embedded Power BI Reports

- **Category:** business
- **Status:** active
- **Owners:** @milliman-lts/platform-engineering

## Summary

Tenant-admin surfaces **Power BI Embedded** reports (cost management / billing) inside the React SPA, gated by the `Reporting` feature flag and the `CostManagement.Reports.View` permission. Embed tokens are minted via the `Integrate.SaaS.Reports` NuGet library.

## Behavior

- `GET api/services/tenant-manager/Reports` — list reports for the caller's `customer` claim.
- `GET api/services/tenant-manager/Reports/{workspaceId:guid}/{reportId:guid}` — get an embed token for a single report.
- `FeatureGate("Reporting")` on the controller — when disabled, `DisabledFeatureHandler` returns 404.
- Authorization policy `CostManagementReader` (permission `CostManagement.Reports.View`).
- SPA renders the report with `powerbi-client-react`.

## Entry Points

- `tenant-admin/src/Integrate.Gateway.TenantManager.Api/Controllers/ReportsController.cs:17` — `ListReports` (L21), `GetReport` (L42).
- `reports/src/Integrate.SaaS.Reports/IEmbeddedReportService.cs:5-36` — library contract.
- `reports/src/Integrate.SaaS.Reports/EmbeddedReportService.cs:11` — implementation.
- `reports/src/Integrate.SaaS.Reports/ServiceCollectionExtensions.cs:9` — `AddEmbeddedReports<TBuilder>`.
- `tenant-admin/src/ClientApp/src/routes/reports.tsx` — SPA route.

## Key Dependencies

- `Microsoft.PowerBI.Api` 4.22.0 — PBI REST.
- `Microsoft.Identity.Client` 4.73.0 — MSAL for PBI bearer tokens.
- `Microsoft.FeatureManagement(.AspNetCore)` 4.3.0 — feature gating.
- `powerbi-client-react` 2.0.2 + `powerbi-models` 2.0.1 (SPA).

## Related Features

- [Feature Flags](./feature-flags.md)
- [Power BI Embedded NuGet Library](../dev/powerbi-embedded-nuget.md)
- [Tenant Administration](./tenant-administration.md)

## Change Log

- 2026-04-21: Seeded.
