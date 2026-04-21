# Shared KeyVault Module

- **Category:** dev
- **Status:** active
- **Owners:** Platform Engineering (LTS)

## Summary

A Terraform module at `shared/modules/KeyVault/` that produces an opinionated, RBAC-authorized Azure Key Vault with diagnostic logs wired to both a Sentinel workspace and a regional Log Analytics workspace. Used by every stack in the repo.

## Behavior

- Creates `azurerm_key_vault` with:
  - `enable_rbac_authorization = true` (no access policies).
  - Purge protection.
  - Network ACLs: default `Deny`, `AzureServices` bypass.
- Accepts a map of role assignments and applies them (`role_assignments.tf`).
- Diagnostic settings: streams KV audit + operational categories to two LA workspace destinations (`diagnostics.tf`).
- Caller supplies tags; module does not invent them.

## Entry Points

- Module root: `shared/modules/KeyVault/main.tf`.
- Role assignments: `shared/modules/KeyVault/role_assignments.tf`.
- Diagnostics: `shared/modules/KeyVault/diagnostics.tf`.
- Variables: `shared/modules/KeyVault/variables.tf`.
- Outputs: `shared/modules/KeyVault/outputs.tf`.
- Module README: `shared/modules/KeyVault/README.md`.

## Key Dependencies

- None (pure azurerm).

## Related Features

- [Shared API Credentials (atmsec)](../business/shared-api-credentials.md)
- [Cert Lifecycle Observability](../business/cert-lifecycle-observability.md)
- [Mandatory Tagging Policy](mandatory-tagging-policy.md)

## Change Log

- 2026-04-21: Seeded.
