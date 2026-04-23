# Sentinel Security Logging

- **Category:** business
- **Status:** active
- **Owners:** Platform Engineering / Security

## Summary

Forwards Front Door access and WAF logs to the dedicated LTS Sentinel Log Analytics workspace (`ltssentinelsecurity`), enabling the security team to detect and investigate threats against the API Foundation.

## Behavior

- A second diagnostic setting on the Front Door profile named `AuditToLTSSentinel` exports exactly two log categories:
  - `FrontDoorAccessLog`
  - `FrontDoorWebApplicationFirewallLog`
- Metrics are **not** forwarded to Sentinel (only to the Integrate workspace).
- APIM diagnostics are **not** forwarded to Sentinel today.
- Default workspace resource ID: `/subscriptions/d53af117-.../workspaces/ltssentinelsecurity` (`variables.tf:38`). Overridable via `sentinel_workspace_id`.

## Entry Points

- `ingress/deploy/afd_diagnostics.tf:23` — `azurerm_monitor_diagnostic_setting.afd_sentinel`.
- `ingress/deploy/variables.tf:38` — `sentinel_workspace_id` variable.

## Key Dependencies

- Azure Sentinel / Log Analytics workspace `ltssentinelsecurity`.
- [WAF Policy & Geo Blocking](waf-policy.md) — produces the WAF events Sentinel consumes.

## Related Features

- [WAF Policy & Geo Blocking](waf-policy.md)
- [Global Azure Front Door Ingress](global-front-door.md)
- [Integrate Workspace Observability](observability-integrate.md)

## Change Log

- 2026-04-21: Seeded.
