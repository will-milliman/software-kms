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
- **Key Vault Watcher** emits structured certificate-expiry and secret-age telemetry for configured vaults to support richer dashboards without relying on long-retention Log Analytics queries (see [Key Vault Watcher Function](../dev/key-vault-watcher-function.md)).
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
- [Key Vault Watcher Function](../dev/key-vault-watcher-function.md).

## Related Features

- [DCV Automation](dcv-automation.md)
- [Hub-Spoke Cert Distribution](hub-spoke-cert-distribution.md)

## Change Log

- 2026-04-21: Seeded.
- 2026-05-07: PR #56 chore(AB#91000): adding some proactive alerting — added proactive observability resources, alerts, and validation for ATMSEC, domain management, and cert-spoke stacks.
- 2026-05-07: PR #67 doc(AB#91000): Adding overview doco for the SSL cert management solution stacks — added architecture, security, observability, and operational runbooks for certificate lifecycle management.
- 2026-05-11: PR #68 feat(AB#91000): granting the grafana managed identity reader access in order to create visuals regarding secret age and certificate expiry — granted the Grafana managed identity reader access across ATMSEC, domain-management, and cert-spoke observability stacks.
- 2026-05-12: PR #70 feat(AB#91000): adding a key vault monitoring function to allow for richer dashboards — added the Key Vault watcher function and observability stack to emit certificate and secret lifecycle telemetry.
- 2026-05-12: PR #71 chore(AB#91000): giving permissions to the kv watcher function identity to read metadata from the key vaults to support monitoring — granted the watcher managed identity read/metadata access to the dev Key Vaults it monitors.
- 2026-05-12: PR #72 chore(AB#91000): force flush the trace buffer — watcher and DCV telemetry now force-flush before function exit so dashboard data is not dropped by buffered exporters.
