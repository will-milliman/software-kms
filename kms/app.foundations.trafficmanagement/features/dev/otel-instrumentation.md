# OpenTelemetry Instrumentation

- **Category:** dev
- **Status:** active
- **Owners:** Platform Engineering (LTS)

## Summary

Both Python function apps use `azure-monitor-opentelemetry` for auto-instrumentation and a custom `@traced` decorator (in `instrumentation/`) to emit rich spans at handler and action level, making DCV and cert-sync flows traceable in Application Insights.

## Behavior

- `azure-monitor-opentelemetry` is added to `requirements.txt` and initialized at function cold start (picks up `APPLICATIONINSIGHTS_CONNECTION_STRING`).
- The `@traced` decorator wraps handler + action entry points to produce named spans (e.g. `dcv.handler.execute`, `dcv.action.submit_validation`), attaching the domain name as a span attribute.
- Spans flow to the per-env Application Insights instance provisioned by the shared flex-function module.
- CertSync also emits custom metrics/spans from both event and full-sync handlers through shared instrumentation helpers.
- KeyVault watcher and DCV watchdog timer functions force-flush OpenTelemetry providers before exit to avoid losing buffered telemetry from short-lived runs.

## Entry Points

- Watchdog: `domain-management/functions/dcv-watchdog/src/DcvWatchdogFunction/instrumentation/__init__.py`.
- Requirements: `domain-management/functions/dcv-watchdog/src/requirements.txt`.
- Cert sync: shares the pattern; App Insights wired via `ssl-cert-spoke/deploy/observability.tf` + `shared/modules/flex-function-app`.
- Key Vault watcher: `observability/functions/keyvault-watcher/src/KeyVaultWatcherFunction/main.py` and `handler.py`.

## Key Dependencies

- [Shared Flex Function-App Module](shared-flex-function-module.md).
- Azure Application Insights / Log Analytics.

## Related Features

- [Cert Lifecycle Observability](../business/cert-lifecycle-observability.md)
- [DCV Watchdog Function](dcv-watchdog-function.md)
- [Key Vault Watcher Function](key-vault-watcher-function.md)
- [CertSync Functions](cert-sync-functions.md)

## Change Log

- 2026-04-21: Seeded.
- 2026-05-07: PR #55 chore(AB#91000): adding some otel to the sync function — added cert-sync OpenTelemetry configuration, metrics helpers, handler instrumentation, and tests.
- 2026-05-07: PR #56 chore(AB#91000): adding some proactive alerting — introduced a dedicated observability stack with action groups, alert rules, backend config, make targets, and validation.
- 2026-05-07: PR #65 Observability — moved alerting closer to the domain-management and ssl-cert-spoke stacks and refactored shared secret-age alerting.
- 2026-05-07: PR #66 refactor(AB#91000): removing the dependency on long retention LAW logs for secret age alerting — removed the LAW-based secret-age alert module pending a function-based replacement.
- 2026-05-12: PR #70 feat(AB#91000): adding a key vault monitoring function to allow for richer dashboards — added structured Key Vault lifecycle telemetry via the watcher function.
- 2026-05-12: PR #72 chore(AB#91000): force flush the trace buffer — DCV and watcher timer functions now force-flush OTEL traces/logs before exit.
