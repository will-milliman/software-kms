# WAF Policy & Geo Blocking

- **Category:** business
- **Status:** active
- **Owners:** Platform Engineering

## Summary

A single AFD firewall policy named `globalapiwafpolicy` runs Microsoft's Default Rule Set 2.1 and Bot Manager 1.1 in **Log (Detection)** mode, and enforces two custom **Block** rules: one for HTTP `TRACE` requests and one for source IPs geolocated in sanctioned countries.

## Behavior

- **Managed rules (Log only):**
  - `DefaultRuleSet` v2.1, action `Log`
  - `Microsoft_BotManagerRuleSet` v1.1, action `Log`
- **Custom rule `block-trace-method` (priority 110, Block):** matches `RequestMethod == TRACE`.
- **Custom rule `BlockedGeographies` (priority 10000, Block):** matches `SocketAddr GeoMatch` against `BY, CN, CU, IR, KP, RU, SY`.
- Blocked responses return HTTP `403` with a static HTML body (base64-encoded "Hello world" placeholder in `afd_waf.tf:9`).
- The policy is bound to `api.<base_domain_name>` for `/*` via the security policy.
- Comment in code flags a follow-up: "Need to add custom rules for allows Azure services/regions" (`afd_waf.tf:59`).

## Entry Points

- `ingress/deploy/afd_waf.tf:1` — `azurerm_cdn_frontdoor_firewall_policy.domain`.
- `ingress/deploy/afd_waf.tf:23` — TRACE block rule.
- `ingress/deploy/afd_waf.tf:37` — geo block rule.
- `ingress/deploy/afd_domain.tf:23` — security policy attaching the WAF to the custom domain.
- `ingress/deploy/context.tf:5` — hard-coded policy name `globalapiwafpolicy`.

## Key Dependencies

- `hashicorp/azurerm` — `azurerm_cdn_frontdoor_firewall_policy`, `azurerm_cdn_frontdoor_security_policy`.
- Front Door Premium SKU — required for managed rule sets and bot manager.

## Related Features

- [Global Azure Front Door Ingress](global-front-door.md)
- [API Custom Domain & TLS](api-custom-domain.md)
- [Sentinel Security Logging](observability-sentinel.md) — captures WAF events.

## Change Log

- 2026-04-21: Seeded.
