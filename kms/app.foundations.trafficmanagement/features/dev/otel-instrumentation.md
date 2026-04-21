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

## Entry Points

- Watchdog: `domain-management/functions/dcv-watchdog/src/DcvWatchdogFunction/instrumentation/__init__.py`.
- Requirements: `domain-management/functions/dcv-watchdog/src/requirements.txt`.
- Cert sync: shares the pattern; App Insights wired via `ssl-cert-spoke/deploy/observability.tf` + `shared/modules/flex-function-app`.

## Key Dependencies

- [Shared Flex Function-App Module](shared-flex-function-module.md).
- Azure Application Insights / Log Analytics.

## Related Features

- [Cert Lifecycle Observability](../business/cert-lifecycle-observability.md)
- [DCV Watchdog Function](dcv-watchdog-function.md)
- [CertSync Functions](cert-sync-functions.md)

## Change Log

- 2026-04-21: Seeded.
