# DNS Made Easy Record Publishing

- **Category:** business
- **Status:** active
- **Owners:** Platform Engineering

## Summary

Publishes the CNAME `api.<base_domain_name>` to the Azure Front Door endpoint hostname in DNS Made Easy, making the API Foundation's custom domain resolvable to end clients.

## Behavior

- Looks up the DME domain (`data.dme_domain.domain`) by name = `var.base_domain_name`.
- Creates a CNAME record with `ttl = 300` from `api.<base_domain_name>` to `azurerm_cdn_frontdoor_endpoint.endpoint.host_name`.
- DME credentials (`DME_API_KEY`, `DME_SECRET_KEY`) come from environment variables populated by the make layer from Key Vault `connsec-<env>` (see [DME Secrets from Key Vault](../dev/secrets-from-keyvault.md)).

## Entry Points

- `ingress/deploy/dns.tf:1` — `data "dme_domain" "domain"`.
- `ingress/deploy/dns.tf:5` — `resource "dme_dns_record" "record"`.
- `ingress/deploy/provider.tf:22` — `dnsmadeeasy/dme` provider pin (`~> 1.0.8`).
- `ingress/deploy/provider.tf:42` — empty `provider "dme"` block with an explanatory comment about credential handling.

## Key Dependencies

- `dnsmadeeasy/dme` Terraform provider — DNS record CRUD.
- `connsec-<env>` Key Vault — source of DME API credentials.
- [API Custom Domain & TLS](api-custom-domain.md) — the hostname being published.

## Related Features

- [API Custom Domain & TLS](api-custom-domain.md)
- [DME Secrets from Key Vault](../dev/secrets-from-keyvault.md)

## Change Log

- 2026-04-21: Seeded.
