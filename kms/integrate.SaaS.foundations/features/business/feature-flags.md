# Feature Flags (Business)

- **Category:** business
- **Status:** active
- **Owners:** @milliman-lts/platform-engineering

## Summary

Selected user-visible capabilities are hidden behind **feature flags** (Microsoft.FeatureManagement) so they can be turned off per environment without redeploying. The canonical example is `Reporting`, which gates the Power BI embedded reports tab in the tenant-admin SPA and the corresponding API.

## Behavior

- Features declared centrally in `tenant-admin/src/Integrate.Gateway.TenantManager.Api/FeatureFlags.cs`.
- Controllers decorated with `[FeatureGate("Reporting")]` — when the flag is off, `DisabledFeatureHandler` responds with **404 Not Found** so the feature is invisible to clients.
- Flag state read from configuration (appsettings / Azure App Configuration).

## Entry Points

- `tenant-admin/src/Integrate.Gateway.TenantManager.Api/Controllers/ReportsController.cs:16` — `[FeatureGate(FeatureFlags.Reporting)]`.
- `tenant-admin/src/Integrate.Gateway.TenantManager.Api/Program.cs:25-26` — `DisabledFeatureHandler` registration.
- `tenant-admin/src/Integrate.Gateway.TenantManager.Api/FeatureFlags.cs` — constants.

## Key Dependencies

- `Microsoft.FeatureManagement.AspNetCore` 4.3.0.

## Related Features

- [Feature Flags (Dev)](../dev/feature-flags.md)
- [Embedded Power BI Reports](./embedded-powerbi-reports.md)

## Change Log

- 2026-04-21: Seeded.
