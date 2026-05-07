# Regional APIM Gateways

- **Category:** business
- **Status:** active
- **Owners:** Platform Engineering

## Summary

Provisions API Management as the regional gateway behind Azure Front Door. The spike has moved toward a private endpoint path between AFD and APIM instead of the earlier regional module abstraction.

## Behavior

- SKU: `Developer_1` (non-HA; one unit per region).
- Publisher metadata: `Milliman LTS` / `jonathan.scutt@milliman.com`.
- `virtual_network_type = "None"` and `public_network_access_enabled = true` on create — commented code shows Internal VNet integration is planned (`apim.tf:18`, `:28`).
- `min_api_version = "2019-12-01"`.
- APIM, AFD origin, provider, and DNS resources now live directly in the ingress root instead of the removed `modules/regional_apim` folder.
- Private endpoint integration between AFD and APIM is represented in the Terraform spike and supporting prompts/skills document APIM onboarding and AFD routing operations.

## Entry Points

- `ingress/deploy/apim.tf` — APIM resource.
- `ingress/deploy/afd_origin.tf` — AFD origin wiring to APIM.
- `ingress/deploy/afd.tf`, `afd_domain.tf`, `afd_route.tf` — Front Door profile, custom domain, and route.
- `ingress/deploy/provider.tf` — provider configuration for the spike.
- `.github/prompts/apim-onboarding.prompt.md` and `.github/skills/apim-traffic-onboarding/SKILL.md` — APIM onboarding guidance.

## Key Dependencies

- `hashicorp/azurerm` — `azurerm_api_management`, `azurerm_user_assigned_identity`, `azurerm_cdn_frontdoor_origin`.
- [Global Azure Front Door Ingress](global-front-door.md) — origin group and route.
- [Integrate Workspace Observability](observability-integrate.md) — APIM diagnostic settings.

## Related Features

- [Global Azure Front Door Ingress](global-front-door.md)
- [Integrate Workspace Observability](observability-integrate.md)

## Change Log

- 2026-04-21: Seeded.
- 2026-05-07: PR #5 Extend the api ingress spike further to include created the private endpoint between the AFD and APIM — reworked APIM/AFD ingress Terraform toward a private endpoint path, removed the old regional APIM module, and added APIM/AFD onboarding agents, prompts, skills, and instructions.
