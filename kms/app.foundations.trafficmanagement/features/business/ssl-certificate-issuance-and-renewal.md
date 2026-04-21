# SSL Certificate Issuance & Renewal

- **Category:** business
- **Status:** active (Incubating stack)
- **Owners:** Platform Engineering (LTS)

## Summary

DigiCert OV (Organization Validation) SSL/TLS certificates are automatically issued and renewed for LTS public domains. Consuming app teams never touch DigiCert, never paste CSRs, and never wrangle DCV TXT records — they just read the cert from their local (spoke) Key Vault.

## Behavior

- Each domain is declared as an `azurerm_key_vault_certificate` in the hub KV, tied to the DigiCert issuer.
- DigiCert issues using DNS-TXT DCV (`_dnsauth.<domain>` TXT record in DNS Made Easy).
- KV auto-renewal is enabled; renewal triggers on approaching expiry.
- The DCV Watchdog closes the DCV loop without human action.
- Renewed certs flow to spokes automatically via Event Grid (see Hub-Spoke Cert Distribution).

## Entry Points

- Hub cert declarations: `domain-management/deploy/certificates.tf`.
- DigiCert issuer (uses `atmsec` creds): `domain-management/deploy/certificates.tf`.
- Per-domain module: `domain-management/deploy/modules/domain/main.tf`.
- Per-env domain lists: `domain-management/configuration/{dev,prod}.json`.

## Key Dependencies

- [Shared API Credentials (atmsec)](shared-api-credentials.md) — DigiCert API key, org ID, account ID.
- [DCV Automation](dcv-automation.md) — closes the DCV TXT loop.
- [Hub-Spoke Cert Distribution](hub-spoke-cert-distribution.md) — delivers the cert to consumers.

## Related Features

- [Domain Onboarding](domain-onboarding.md)
- [Cert Lifecycle Observability](cert-lifecycle-observability.md)

## Change Log

- 2026-04-21: Seeded.
