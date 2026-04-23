# Hub-Spoke Cert Distribution

- **Category:** business
- **Status:** active (Incubating)
- **Owners:** Platform Engineering (LTS)
- **Straddles:** business + dev — see [dev CertSync Functions](../dev/cert-sync-functions.md).

## Summary

Certificates issued or renewed in the central (hub) Key Vault are automatically copied into every registered spoke Key Vault, so application workloads always read a cert from a KV in their own subscription.

## Behavior

- The hub KV has an Event Grid System Topic (`hub-kv-cert-events-<env>`) that emits `Microsoft.KeyVault.CertificateNewVersionCreated` and `CertificateNearExpiry` events.
- Each spoke subscribes its `cert-sync-events` Storage Queue to that topic.
- `CertSyncFunction` picks events off the queue, pulls the latest cert version from hub KV as base64 PKCS12, and imports it into the spoke KV.
- `FullSyncFunction` runs on a configurable timer to catch any events that were lost or preceded the spoke existing.
- Un-processable events land in a DLQ container on the spoke storage account.

## Entry Points

- Hub Event Grid System Topic: `domain-management/deploy/eventgrid.tf`.
- Spoke subscription + queue: `ssl-cert-spoke/deploy/eventgrid.tf`, `queue.tf`.
- Sync logic: `ssl-cert-spoke/functions/cert-sync/src/shared/sync_logic.py`.
- Queue-trigger function: `ssl-cert-spoke/functions/cert-sync/src/CertSyncFunction/main.py`.
- Full-sync function: `ssl-cert-spoke/functions/cert-sync/src/FullSyncFunction/main.py`.

## Key Dependencies

- [CertSync Functions](../dev/cert-sync-functions.md) (dev).
- [Shared Flex Function-App Module](../dev/shared-flex-function-module.md).
- Hub stack RBAC grant to spoke identities (`domain-management/deploy/spoke_permissions.tf`).

## Related Features

- [Multi-Environment Spoke Provisioning](multi-env-spoke-provisioning.md)
- [Spoke Onboarding Runbook](spoke-onboarding-runbook.md)

## Change Log

- 2026-04-21: Seeded.
- 2026-04-21: PR #30 fix(AB#91000): corrected typo in the key vault cert user role — fixed role name in `domain-management/deploy/spoke_permissions.tf`.
