# Feature Flags (Dev)

- **Category:** dev
- **Status:** active
- **Owners:** @milliman-lts/platform-engineering

## Summary

Developer-facing view of feature-flag wiring. Uses **Microsoft.FeatureManagement** with a `DisabledFeatureHandler` that returns `404` so disabled features are invisible to clients.

## Behavior

- Flag names are centralised in `FeatureFlags.cs` constants class.
- Controllers / actions use `[FeatureGate(FeatureFlags.Xxx)]`; disabled gates route to `DisabledFeatureHandler`.
- Flag state read from `IConfiguration` (appsettings / env / Azure App Configuration).
- Adding a new flag: declare constant → gate the endpoint → add default in appsettings → update Aspire parameters if per-env.

## Entry Points

- `tenant-admin/src/Integrate.Gateway.TenantManager.Api/FeatureFlags.cs` — constants.
- `tenant-admin/src/Integrate.Gateway.TenantManager.Api/Program.cs:25-26` — `DisabledFeatureHandler` + `AddFeatureManagement`.
- `tenant-admin/src/Integrate.Gateway.TenantManager.Api/Controllers/ReportsController.cs:16` — usage example.

## Key Dependencies

- `Microsoft.FeatureManagement` 4.3.0.
- `Microsoft.FeatureManagement.AspNetCore` 4.3.0.

## Related Features

- [Feature Flags (Business)](../business/feature-flags.md)

## Change Log

- 2026-04-21: Seeded.
