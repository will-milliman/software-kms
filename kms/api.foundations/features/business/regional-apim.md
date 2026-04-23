# Regional APIM Gateways

- **Category:** business
- **Status:** active
- **Owners:** Platform Engineering

## Summary

Provisions one Azure API Management instance per region in `var.apim_regions` (today: `eastus`, `westus`). Each instance gets a user-assigned managed identity and is registered as an AFD origin in the shared `api-og` origin group, giving the API Foundation geo-redundant gateway capacity behind a single front door.

## Behavior

- SKU: `Developer_1` (non-HA; one unit per region).
- Publisher metadata: `Milliman LTS` / `jonathan.scutt@milliman.com`.
- `virtual_network_type = "None"` and `public_network_access_enabled = true` on create — commented code shows Internal VNet integration is planned (`apim.tf:18`, `:28`).
- `min_api_version = "2019-12-01"`.
- Each APIM has a dedicated user-assigned managed identity `apim-<region>-<env>-identity`.
- Two Checkov rules are explicitly skipped with inline comments: `CKV_AZURE_107` (VNet) and `CKV_AZURE_174` (public access) (`apim.tf:10`).
- The AFD origin derives its hostname by trimming `https://` and trailing `/` from APIM's `gateway_regional_url` (falling back to `gateway_url` on first apply) (`afd_origin.tf:3`).

## Entry Points

- `ingress/deploy/regional_apim.tf:1` — root instantiation of `module.regional_apim` with `for_each = toset(var.apim_regions)`.
- `ingress/deploy/modules/regional_apim/apim.tf:1` — user-assigned identity.
- `ingress/deploy/modules/regional_apim/apim.tf:9` — `azurerm_api_management.apim_internal`.
- `ingress/deploy/modules/regional_apim/afd_origin.tf:9` — `azurerm_cdn_frontdoor_origin.origin`.
- `ingress/deploy/modules/regional_apim/context.tf:2` — naming: `apim-<region>-<env>`.
- `ingress/deploy/modules/regional_apim/outputs.tf:1` — `afd_origin_ids` consumed by the root route.

## Key Dependencies

- `hashicorp/azurerm` — `azurerm_api_management`, `azurerm_user_assigned_identity`, `azurerm_cdn_frontdoor_origin`.
- [Global Azure Front Door Ingress](global-front-door.md) — origin group and route.
- [Integrate Workspace Observability](observability-integrate.md) — APIM diagnostic settings.

## Related Features

- [Global Azure Front Door Ingress](global-front-door.md)
- [Integrate Workspace Observability](observability-integrate.md)

## Change Log

- 2026-04-21: Seeded.
