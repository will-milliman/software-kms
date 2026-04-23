# Global App Ingress (Azure Front Door)

- **Category:** business
- **Status:** active (Incubating)
- **Owners:** Platform Engineering (LTS)

## Summary

A shared Azure Front Door **Premium** profile that acts as the global edge in front of Milliman LTS applications. Gives app teams a consistent, TLS-terminated, globally-routed entry point without each team provisioning their own Front Door.

## Behavior

- Provisions one `azurerm_cdn_frontdoor_profile` (SKU `Premium_AzureFrontDoor`) per deploy.
- Uses a user-assigned managed identity (integration with cert/secret sources when wired).
- Diagnostic settings stream to Log Analytics (`afd_diagnostics.tf`).
- The stack is intentionally minimal today — README flags "TODO add IaC build badge" and the stack is marked Incubating.
- The AFD user-assigned identity is granted **Key Vault Secrets User** on the SSL cert vault (per-env, configured via `ssl_kv_*` variables in `afd.tf` / `variables.tf`) so Front Door can pull TLS certs.

## Entry Points

- Front Door resource: `global-app-ingress/deploy/afd.tf`.
- Diagnostics: `global-app-ingress/deploy/afd_diagnostics.tf`.
- Variables: `global-app-ingress/deploy/variables.tf`.
- Per-env config: `global-app-ingress/configuration/dev.json`.
- Pipeline: `global-app-ingress/.pipelines/infra-deploy.yaml`.

## Key Dependencies

- [Shared API Credentials (atmsec)](shared-api-credentials.md) (indirect).
- [Terraform Four-Stack Layout](../dev/terraform-four-stack-layout.md).

## Related Features

- [SSL Certificate Issuance & Renewal](ssl-certificate-issuance-and-renewal.md) (future: certs attached to FD endpoints).

## Change Log

- 2026-04-21: Seeded.
- 2026-04-21: PR #24 Resolve issue with terraform output.json file not being generated — fixed `global-app-ingress/deploy/outputs.tf` and bumped `cicd-shared` submodule pointer.
- 2026-04-21: PR #31 Extend the global ingress to grant the AFD identity permissions to the ssl kv (AB#90486) — added KV-secrets role assignment for the AFD identity in `afd.tf`, plus new `variables.tf` inputs and `configuration/dev.json` settings for the SSL KV reference.
- 2026-04-22: PR #35 Correct how the principal id is set for the role assignment (AB#90486) — fixed AFD identity principal id wiring in `afd.tf`.
