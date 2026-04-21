# Shared API Credentials (atmsec)

- **Category:** business
- **Status:** Generally Available
- **Owners:** Platform Engineering (LTS)

## Summary

`atmsec` (**A**pp **T**raffic **M**anagement **Sec**rets) is a small, stable Key Vault stack that centrally holds the DigiCert and DNS Made Easy API credentials used by every other traffic-management stack. Rotation happens out-of-band; Terraform owns the secret shape, not its value.

## Behavior

- Provisions one resource group and one Key Vault per env.
- Creates the following secrets (each with initial value `"changeme"` and `lifecycle { ignore_changes = [value] }`):
  - `digicert-org-id`
  - `digicert-api-key`
  - `digicert-account-id`
  - `dme-api-key`
  - `dme-secret-key`
- Grants **Key Vault Secrets User** role to:
  - Platform AAD groups (people who rotate secrets).
  - Workload identities of the DCV Watchdog and other consuming functions.

## Entry Points

- Stack root: `atmsec/deploy/main.tf`.
- Secrets module: `atmsec/deploy/modules/secrets/secrets.tf`.
- Secret declarations: `atmsec/deploy/secrets.tf`.
- Variables: `atmsec/deploy/variables.tf`.
- Per-env config: `atmsec/configuration/dev.json`.
- Pipeline: `atmsec/.pipelines/infra-cd.yaml`.

## Key Dependencies

- [Shared KeyVault Module](../dev/shared-keyvault-module.md).

## Related Features

- [DCV Automation](dcv-automation.md) (primary consumer)
- [SSL Certificate Issuance & Renewal](ssl-certificate-issuance-and-renewal.md)

## Change Log

- 2026-04-21: Seeded.
