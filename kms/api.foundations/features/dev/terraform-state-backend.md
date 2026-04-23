# Terraform State Backend Convention

- **Category:** dev
- **Status:** active
- **Owners:** Platform Engineering

## Summary

Every stack in `api.foundations` uses the `azurerm` backend with Azure AD authentication against a single state storage account in the `lz.data-platform.infra.management` subscription. The state key is `api.foundations/<stack>-<env>.tfstate`, making it trivial to inspect and reason about state blobs across components.

## Behavior

- **Backend block** (`ingress/deploy/provider.tf:4`):
  - `use_azuread_auth = true` (no storage keys).
  - `subscription_id = cc31f800-80a8-4e8b-9cf5-a20a232a5b39` (`lz.data-platform.infra.management`).
  - `tenant_id = da280eb2-7328-4fc8-9521-154de96d70eb`.
  - `container_name = tfstate`.
- **Per-env backend HCL** (`ingress/configuration/<env>.backend.hcl`):
  - `resource_group_name = infrastructure-automation-dev`
  - `storage_account_name = devtfaccount85c403d0`
  - (Both `dev.backend.hcl` and `plt.backend.hcl` point to the same account today.)
- **State key** (`ingress/deploy/make.mk:8`): `api.foundations/${STACK_NAME}-${ENVIRONMENT}.tfstate`.
- The `azurerm` provider also sets `storage_use_azuread = true` and `resource_provider_registrations = "none"` to avoid RP registration side effects on least-privilege principals (`ingress/deploy/provider.tf:32`).

## Entry Points

- `ingress/deploy/provider.tf:4` — backend block.
- `ingress/deploy/make.mk:7` — state key and backend config file wiring.
- `ingress/configuration/dev.backend.hcl`, `ingress/configuration/plt.backend.hcl`.

## Key Dependencies

- Azure Storage account `devtfaccount85c403d0` and container `tfstate`.
- Azure AD principals (developer accounts or pipeline service connections) with `Storage Blob Data Contributor` on the container.

## Related Features

- [Make Command Protocol](make-command-protocol.md)
- [Azure DevOps Pipeline (ingress)](ado-pipeline-ingress.md)

## Change Log

- 2026-04-21: Seeded.
