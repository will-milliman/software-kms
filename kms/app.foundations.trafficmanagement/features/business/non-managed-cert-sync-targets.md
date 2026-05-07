# Non-Managed Certificate Sync Targets

- **Category:** business
- **Status:** active
- **Owners:** Platform Engineering (LTS)

## Summary

CertSync can distribute hub-managed certificates into additional target Key Vaults that are not managed by the standard spoke stack, covering ClickOps or separately managed application vaults.

## Behavior

- Spoke configuration can declare extra target Key Vaults outside the managed spoke resources.
- CertSync and FullSync use shared sync logic so event-driven and timer-driven paths both honor non-managed targets.
- Additional role assignments grant the spoke function access to those target vaults.
- Runbooks document the access-grant flow and failure investigation process.

## Entry Points

- `ssl-cert-spoke/functions/cert-sync/src/shared/config.py` — target configuration parsing.
- `ssl-cert-spoke/functions/cert-sync/src/shared/sync_logic.py` — shared copy logic.
- `ssl-cert-spoke/deploy/permissions.tf` — target-vault access.
- `ssl-cert-spoke/docs/runbooks/granting-certsync-access-to-non-managed-keyvaults.md`.

## Key Dependencies

- [CertSync Functions](../dev/cert-sync-functions.md)
- Azure Key Vault RBAC or access policy grants on external target vaults.

## Related Features

- [Hub-Spoke Cert Distribution](./hub-spoke-cert-distribution.md)
- [Cert Lifecycle Observability](./cert-lifecycle-observability.md)

## Change Log

- 2026-05-07: PR #58 feat(AB#91000): Adding capability to sync certs to non-managed key vaults — CertSync can now target externally managed/ClickOps vaults via configuration, access grants, and shared sync logic.
