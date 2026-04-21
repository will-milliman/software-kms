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
