# Global Azure Front Door Ingress

- **Category:** business
- **Status:** active
- **Owners:** Platform Engineering

## Summary

Provisions an Azure Front Door **Premium** profile, endpoint, origin group, and route that together serve as the global entry point for LTS APIs. All regional APIM gateways register as origins in the single `api-og` origin group, and a single catch-all route (`/*`) forwards traffic with HTTPS enforcement.

## Behavior

- One AFD endpoint fronts all regions; Microsoft's anycast network picks the closest PoP.
- Load balancing uses `sample_size=4`, `successful_samples_required=3`, `additional_latency_in_milliseconds=0`.
- Response timeout is raised to `240` seconds to accommodate long-running API calls (`afd.tf:5`).
- Health probes for the origin group are currently commented out (`afd.tf:27`) — open question in code.
- Traffic patterns matched: `/*`. Protocols: HTTP + HTTPS (with forced redirect). Forwarding protocol: HTTPS only.

## Entry Points

- `ingress/deploy/afd.tf:1` — `azurerm_cdn_frontdoor_profile.afd` (Premium SKU).
- `ingress/deploy/afd.tf:10` — `azurerm_cdn_frontdoor_endpoint.endpoint`.
- `ingress/deploy/afd.tf:17` — `azurerm_cdn_frontdoor_origin_group.api` (name: `api-og`).
- `ingress/deploy/afd_route.tf:1` — `azurerm_cdn_frontdoor_route.api`; gathers origin IDs via `flatten([for m in values(module.regional_apim) : m.afd_origin_ids])`.

## Key Dependencies

- `hashicorp/azurerm` Front Door resources (Premium SKU required for customer certificates and security policies).
- [Regional APIM Gateways](regional-apim.md) — supplies the origins.
- [API Custom Domain & TLS](api-custom-domain.md) — terminates the customer hostname.

## Related Features

- [Regional APIM Gateways](regional-apim.md)
- [WAF Policy & Geo Blocking](waf-policy.md)
- [HTTPS-only Enforcement](https-enforcement.md)

## Change Log

- 2026-04-21: Seeded.
