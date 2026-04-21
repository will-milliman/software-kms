# Cert Lifecycle Observability

- **Category:** business
- **Status:** active
- **Owners:** Platform Engineering (LTS) / Security Operations

## Summary

Every Key Vault, function, and ingress resource in the traffic-management foundation streams logs and metrics to Sentinel and Log Analytics, and raises metric alerts, so the SecOps and platform teams can audit cert lifecycle events and catch automation failures early.

## Behavior

- **Key Vaults** (via `shared/modules/KeyVault`): diagnostic settings forward audit + operations logs to:
  - A central Sentinel Log Analytics workspace.
  - A regional Log Analytics workspace.
- **Functions** (via `shared/modules/flex-function-app`): Application Insights + metric alerts on failure rate, 5xx rate, memory usage.
- **DCV Watchdog + CertSync functions** emit custom OpenTelemetry spans via a `@traced` decorator (see [OpenTelemetry Instrumentation](../dev/otel-instrumentation.md)).
- **Front Door** (GAI) has diagnostic settings to Log Analytics (`afd_diagnostics.tf`).

## Entry Points

- KV diagnostics template: `shared/modules/KeyVault/diagnostics.tf`.
- Function metric alerts: `shared/modules/flex-function-app/metric_alerts.tf`.
- Hub observability wiring: `domain-management/deploy/observability.tf`.
- Spoke observability wiring: `ssl-cert-spoke/deploy/observability.tf`.
- GAI diagnostics: `global-app-ingress/deploy/afd_diagnostics.tf`.

## Key Dependencies

- [Shared KeyVault Module](../dev/shared-keyvault-module.md).
- [Shared Flex Function-App Module](../dev/shared-flex-function-module.md).
- [OpenTelemetry Instrumentation](../dev/otel-instrumentation.md).

## Related Features

- [DCV Automation](dcv-automation.md)
- [Hub-Spoke Cert Distribution](hub-spoke-cert-distribution.md)

## Change Log

- 2026-04-21: Seeded.
