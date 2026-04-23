# Integrate Workspace Observability (dev view)

- **Category:** dev
- **Status:** active
- **Owners:** Platform Engineering

## Summary

Developer-facing side of the Integrate Log Analytics wiring: this is how engineers debug AFD routing and APIM requests. Every diagnostic category the Azure resource emits is enabled, so queries against `AzureDiagnostics` / resource-specific tables in `integrate-monitoring-us` see the full fidelity.

## Behavior

- Front Door diagnostic setting `AuditToLTSLA` (`ingress/deploy/afd_diagnostics.tf:5`):
  - `enabled_log` iterates `data.azurerm_monitor_diagnostic_categories.afd.log_category_types` — i.e., "enable everything the resource supports".
  - `enabled_metric { category = "AllMetrics" }`.
- APIM per-region diagnostic setting `AuditToLTSLA` (`ingress/deploy/modules/regional_apim/diagnostics.tf:5`): same "enable everything" pattern for APIM log categories plus `AllMetrics`.
- Workspace resource ID lives in `integrate_workspace_id` with a default subscription in `variables.tf:22`; overridable per environment.
- Useful tables to query in Log Analytics:
  - `AzureDiagnostics` filtered by `ResourceProvider == "MICROSOFT.CDN"` for Front Door.
  - `AzureDiagnostics` filtered by `ResourceProvider == "MICROSOFT.APIMANAGEMENT"` for APIM.
  - `AzureMetrics` for latency/throughput.

## Entry Points

- `ingress/deploy/afd_diagnostics.tf:1` — categories data source for AFD.
- `ingress/deploy/afd_diagnostics.tf:5` — AFD → Integrate LA diagnostic setting.
- `ingress/deploy/modules/regional_apim/diagnostics.tf:1` — APIM categories data source.
- `ingress/deploy/modules/regional_apim/diagnostics.tf:5` — APIM → Integrate LA diagnostic setting.

## Key Dependencies

- Log Analytics workspace `integrate-monitoring-us`.
- `hashicorp/azurerm` provider.

## Related Features

- Business-side view: [Integrate Workspace Observability (business)](../business/observability-integrate.md)
- [Sentinel Security Logging](../business/observability-sentinel.md)
- [Regional APIM Gateways](../business/regional-apim.md)
- [Global Azure Front Door Ingress](../business/global-front-door.md)

## Change Log

- 2026-04-21: Seeded.
