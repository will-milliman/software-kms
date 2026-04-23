# API Custom Domain & TLS

- **Category:** business
- **Status:** active
- **Owners:** Platform Engineering

## Summary

Exposes the public hostname `api.<base_domain_name>` (e.g., `api.plt.integrate-solutions.com`) on Azure Front Door with a customer-managed TLS certificate pulled from Azure Key Vault. This is the single branded entry point that API consumers hit.

## Behavior

- Clients reach APIs at `https://api.<base_domain_name>/...`.
- Front Door terminates TLS using the certificate referenced by `base_domain_name_certificate_key_vault_id`.
- The default `*.azurefd.net` hostname is **disabled** (`link_to_default_domain = false`), so the custom domain is the only entry point.
- A security policy binds the WAF to this domain for all paths (`/*`).

## Entry Points

- `ingress/deploy/afd_domain.tf:1` — `azurerm_cdn_frontdoor_custom_domain.domain` declares the hostname and ties it to the customer certificate.
- `ingress/deploy/afd_domain.tf:12` — `azurerm_cdn_frontdoor_secret.domain` wraps the Key Vault certificate reference for AFD.
- `ingress/deploy/afd_domain.tf:23` — `azurerm_cdn_frontdoor_security_policy.domain` attaches the WAF to the domain.
- `ingress/deploy/afd_route.tf:12` — `link_to_default_domain = false`.
- `ingress/deploy/context.tf:3` — `local.domain_name = "api.${var.base_domain_name}"`.

## Key Dependencies

- `hashicorp/azurerm` — Front Door custom domain, secret, and security policy resources.
- Azure Key Vault — source of the TLS certificate (e.g., `sslcertspokegitopsdev` in dev).
- [DNS Made Easy Record Publishing](dns-management.md) — provides the CNAME that routes the hostname to Front Door.

## Related Features

- [Global Azure Front Door Ingress](global-front-door.md)
- [WAF Policy & Geo Blocking](waf-policy.md)
- [DNS Made Easy Record Publishing](dns-management.md)
- [HTTPS-only Enforcement](https-enforcement.md)

## Change Log

- 2026-04-21: Seeded.
