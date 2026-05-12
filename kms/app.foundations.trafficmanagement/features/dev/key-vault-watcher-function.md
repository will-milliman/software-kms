# Key Vault Watcher Function

- **Category:** dev
- **Status:** active (Incubating)
- **Owners:** Platform Engineering (LTS)
- **Straddles:** business + dev — see [business Cert Lifecycle Observability](../business/cert-lifecycle-observability.md).

## Summary

A Python 3.12 Azure Function, timer-triggered hourly, that scans configured Key Vaults and emits structured telemetry for certificate expiry and secret age into Application Insights.

## Behavior

- Reads `KEY_VAULTS` JSON config with per-vault `observe_certificates` and `observe_secrets` flags.
- Uses managed identity with Key Vault Reader and Key Vault Certificate User RBAC grants on watched vaults.
- Emits `kv_certificate_status` records with vault name, certificate name, expiry date, days until expiry, and enabled state.
- Emits `kv_secret_status` records with vault name, secret name, last-updated timestamp, age in days, and enabled state.
- Accumulates failed vaults and raises after processing the rest, so partial telemetry still flows but the function run signals failure.
- Force-flushes OpenTelemetry logs/traces before function exit so short timer runs are not lost to buffered exporters.

## Entry Points

- Function source: `observability/functions/keyvault-watcher/src/KeyVaultWatcherFunction/`.
- Entry: `main.py`, `function.json`.
- Handler: `handler.py`.
- Config: `config.py`.
- Terraform: `observability/deploy/kv_watcher_func.tf`.
- Pipelines: `observability/.pipelines/function-cd.yaml`, `observability/.pipelines/infra-cd.yaml`.
- Make targets: `observability/functions/make.mk`, exposed through `make observability__app__*`.

## Key Dependencies

- [Shared Flex Function-App Module](shared-flex-function-module.md).
- [OpenTelemetry Instrumentation](otel-instrumentation.md).
- Azure Key Vault certificates/secrets SDK clients.
- Managed identity RBAC on watched hub, spoke, and ATMSEC vaults.

## Related Features

- [Cert Lifecycle Observability](../business/cert-lifecycle-observability.md)
- [DCV Watchdog Function](dcv-watchdog-function.md)

## Change Log

- 2026-05-12: PR #70 feat(AB#91000): adding a key vault monitoring function to allow for richer dashboards — added the observability stack function, Terraform, pipelines, Docker/Make build targets, and tests.
- 2026-05-12: PR #71 chore(AB#91000): giving permissions to the kv watcher function identity to read metadata from the key vaults to support monitoring — configured dev RBAC principal IDs for ATMSEC, domain-management, and ssl-cert-spoke vaults.
- 2026-05-12: PR #72 chore(AB#91000): force flush the trace buffer — made watcher telemetry flush before timer-function exit and refined structured log dimensions.
