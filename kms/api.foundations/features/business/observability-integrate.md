# Integrate Workspace Observability (business view)

- **Category:** business
- **Status:** active
- **Owners:** Platform Engineering

## Summary

All ingress components stream diagnostic logs and metrics to the shared Integrate Log Analytics workspace (`integrate-monitoring-us`), providing the operational telemetry that product teams and operators rely on to monitor API health, latency, and availability.

## Behavior

- Front Door: every available diagnostic log category is enabled plus `AllMetrics`, with the setting named `AuditToLTSLA` (`afd_diagnostics.tf:5`).
- APIM (per region): every available diagnostic log category is enabled plus `AllMetrics`, named `AuditToLTSLA` (`modules/regional_apim/diagnostics.tf:5`).
- Default workspace resource ID: `/subscriptions/932ac68a-.../workspaces/integrate-monitoring-us` (`variables.tf:22`). Overridable via `integrate_workspace_id`.

## Entry Points

- `ingress/deploy/afd_diagnostics.tf:1` — diagnostic categories data source for Front Door.
- `ingress/deploy/afd_diagnostics.tf:5` — `azurerm_monitor_diagnostic_setting.health_metrics`.
- `ingress/deploy/modules/regional_apim/diagnostics.tf:5` — APIM diagnostic setting.
- `ingress/deploy/variables.tf:22` — `integrate_workspace_id` variable.

## Key Dependencies

- Azure Log Analytics workspace `integrate-monitoring-us`.
- `hashicorp/azurerm` — `azurerm_monitor_diagnostic_setting`, `azurerm_monitor_diagnostic_categories` data source.

## Related Features

- [Global Azure Front Door Ingress](global-front-door.md)
- [Regional APIM Gateways](regional-apim.md)
- [Sentinel Security Logging](observability-sentinel.md)
- Dev-side view: [Integrate Workspace Observability (dev view)](../dev/observability-integrate.md)

## Change Log

- 2026-04-21: Seeded.
