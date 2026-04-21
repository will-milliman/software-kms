# Shared Flex Function-App Module

- **Category:** dev
- **Status:** active
- **Owners:** Platform Engineering (LTS)

## Summary

A Terraform module at `shared/modules/flex-function-app/` that provisions a Python Flex-Consumption Azure Function App with SystemAssigned identity, `WEBSITE_RUN_FROM_PACKAGE` blob-based deployment, portal CORS, AzureCloud-only IP restriction, and metric alerts. Used for the DCV Watchdog and both CertSync functions.

## Behavior

- Creates:
  - A dedicated storage account (for function host, package zip, and keys).
  - An `azurerm_service_plan` with `sku_name = "FC1"` (Flex Consumption, Linux).
  - `azurerm_linux_function_app` with:
    - SystemAssigned managed identity.
    - Python 3.12 runtime.
    - `WEBSITE_RUN_FROM_PACKAGE` pointing at a blob URL (deploy = upload zip + restart).
    - CORS allowing `https://portal.azure.com`.
    - IP restriction to AzureCloud service tag.
    - Caller-supplied app settings map.
  - Metric alerts for failure rate / 5xx / memory (`metric_alerts.tf`).
  - RBAC grants for the function MI on its own storage (`function_app_keys.tf`).

## Entry Points

- Module root: `shared/modules/flex-function-app/function.tf`.
- Variables: `shared/modules/flex-function-app/variables.tf`.
- Storage: `shared/modules/flex-function-app/storage.tf`.
- Keys: `shared/modules/flex-function-app/function_app_keys.tf`.
- Metric alerts: `shared/modules/flex-function-app/metric_alerts.tf`.
- Outputs: `shared/modules/flex-function-app/output.tf`.
- README: `shared/modules/flex-function-app/README.md`.

## Key Dependencies

- None (pure azurerm).

## Related Features

- [DCV Watchdog Function](dcv-watchdog-function.md)
- [CertSync Functions](cert-sync-functions.md)
- [Cert Lifecycle Observability](../business/cert-lifecycle-observability.md)

## Change Log

- 2026-04-21: Seeded.
