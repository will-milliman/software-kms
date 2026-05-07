# CertSync Functions

- **Category:** dev
- **Status:** active (Incubating)
- **Owners:** Platform Engineering (LTS)
- **Straddles:** business + dev — see [business Hub-Spoke Cert Distribution](../business/hub-spoke-cert-distribution.md).

## Summary

Two Python 3.12 Azure Functions running on the spoke that keep the spoke Key Vault in sync with the hub: `CertSyncFunction` (queue-triggered per event) and `FullSyncFunction` (timer-triggered safety net). Both live in the same function app package and share a `shared/` helper module.

## Behavior

### `CertSyncFunction`

- Trigger: Storage Queue `cert-sync-events`.
- Event types handled: `Microsoft.KeyVault.CertificateNewVersionCreated`, `Microsoft.KeyVault.CertificateNearExpiry`.
- Parses subject `certificates/<name>` → looks up the hub cert → downloads PKCS12 base64 → imports into spoke KV with empty password.

### `FullSyncFunction`

- Trigger: Timer, schedule from `%FULL_SYNC_SCHEDULE%` app setting.
- Enumerates every cert in hub KV, compares `updated_on` against the spoke copy, syncs any that are newer or missing.
- Safety net for missed/dropped Event Grid events.
- Both event-driven and full-sync paths can sync to additional non-managed Key Vault targets configured outside the standard spoke stack.

### Shared

- `shared/sync_logic.py` — the actual copy function used by both.
- `shared/keyvault_client.py` — thin wrapper over `SecretClient` + `CertificateClient`.
- `shared/config.py` — env-var parsing.

## Entry Points

- Function source: `ssl-cert-spoke/functions/cert-sync/src/`.
- `CertSyncFunction/main.py`, `function.json`.
- `FullSyncFunction/main.py`, `function.json`.
- `shared/sync_logic.py`, `shared/keyvault_client.py`, `shared/config.py`.
- Terraform: `ssl-cert-spoke/deploy/cert_sync_func.tf`.
- Queue + DLQ: `ssl-cert-spoke/deploy/queue.tf`.
- Event Grid subscription: `ssl-cert-spoke/deploy/eventgrid.tf`.
- Pipeline: `ssl-cert-spoke/.pipelines/function-cd.yaml`.

## Key Dependencies

- [Shared Flex Function-App Module](shared-flex-function-module.md).
- Hub Event Grid system topic (`domain-management/deploy/eventgrid.tf`).
- Spoke workload identity granted Secrets User on hub KV (`domain-management/deploy/spoke_permissions.tf`).

## Related Features

- [business Hub-Spoke Cert Distribution](../business/hub-spoke-cert-distribution.md)
- [OpenTelemetry Instrumentation](otel-instrumentation.md)

## Change Log

- 2026-04-21: Seeded.
- 2026-05-07: PR #54 chore(AB#91000): readding in the code to wire to the LAW — cert-sync function wiring now emits to the Log Analytics/Application Insights observability path.
- 2026-05-07: PR #55 chore(AB#91000): adding some otel to the sync function — cert-sync functions gained OpenTelemetry setup, custom metrics/spans, and instrumentation tests.
- 2026-05-07: PR #58 feat(AB#91000): Adding capability to sync certs to non-managed key vaults — cert-sync shared logic now supports external/non-managed target vaults with config, permissions, and tests.
