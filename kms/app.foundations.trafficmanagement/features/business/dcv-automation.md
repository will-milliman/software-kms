# DCV Automation

- **Category:** business
- **Status:** active (Incubating)
- **Owners:** Platform Engineering (LTS)
- **Straddles:** business + dev — see [dev DCV Watchdog Function](../dev/dcv-watchdog-function.md).

## Summary

Domain Control Validation — DigiCert's proof-of-domain-ownership step — is closed end-to-end by the **DCV Watchdog** Azure Function. App teams and platform operators never paste `_dnsauth.*` TXT records by hand.

## Behavior

- Daily (CRON `0 0 * * *`) the Watchdog inspects every managed domain.
- For any domain that needs DCV (new, pending, or near expiry) it:
  1. Reads the token from DigiCert.
  2. Writes the TXT record at `_dnsauth.<domain>` into DNS Made Easy.
  3. Stores the token + a `requested-at` timestamp as secrets in the hub KV.
  4. Asks DigiCert to check.
- If validation stays pending longer than `PENDING_VALIDATION_TIMEOUT_HOURS` (default 24), it resubmits.
- After `CLEANUP_TXT_PERIOD_HOURS` (default 48) it removes the TXT record to keep DNS clean.
- `DRY_RUN=true` by default — flipped explicitly per env.

## Entry Points

- Watchdog source: `domain-management/functions/dcv-watchdog/src/DcvWatchdogFunction/`.
- Handler: `handler.py:DcvWatchdogHandler.execute`.
- State machine: `domain_state.py`.
- Action handlers: `actions/{add_domain,activate_domain,submit_validation,cleanup_dcv_records}.py`.
- Infra: `domain-management/deploy/dcv_watchdog_func.tf`.

## Key Dependencies

- [DigiCert v2 API Client](../dev/digicert-v2-client.md).
- [DNS Made Easy API Client](../dev/dns-made-easy-client.md).
- [Shared API Credentials (atmsec)](shared-api-credentials.md).
- [DCV State Machine](../dev/dcv-state-machine.md).

## Related Features

- [SSL Certificate Issuance & Renewal](ssl-certificate-issuance-and-renewal.md)
- [Cert Lifecycle Observability](cert-lifecycle-observability.md)

## Change Log

- 2026-04-21: Seeded.
