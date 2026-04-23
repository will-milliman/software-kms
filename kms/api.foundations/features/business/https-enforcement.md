# HTTPS-only Enforcement

- **Category:** business
- **Status:** active
- **Owners:** Platform Engineering

## Summary

Front Door forces all inbound traffic to HTTPS and forwards to origins over HTTPS only. Clients that request `http://api.<base_domain_name>/...` are redirected; the default `*.azurefd.net` hostname is disabled to prevent bypass.

## Behavior

- `supported_protocols = ["Http", "Https"]` so AFD accepts HTTP in order to redirect it.
- `https_redirect_enabled = true` — HTTP requests receive a redirect to HTTPS.
- `forwarding_protocol = "HttpsOnly"` — origin (APIM) is never contacted over plaintext.
- `link_to_default_domain = false` — the AFD default hostname cannot be used to reach the route; only the custom domain works.
- Origin certificate name check is enabled (`certificate_name_check_enabled = true`) so APIM certs are validated.

## Entry Points

- `ingress/deploy/afd_route.tf:9` — `supported_protocols`, `forwarding_protocol`, `https_redirect_enabled`, `link_to_default_domain`.
- `ingress/deploy/modules/regional_apim/afd_origin.tf:17` — `certificate_name_check_enabled`.

## Key Dependencies

- [API Custom Domain & TLS](api-custom-domain.md) — supplies the customer cert terminating TLS at AFD.
- [Global Azure Front Door Ingress](global-front-door.md) — hosts the route where these settings live.

## Related Features

- [API Custom Domain & TLS](api-custom-domain.md)
- [Global Azure Front Door Ingress](global-front-door.md)

## Change Log

- 2026-04-21: Seeded.
