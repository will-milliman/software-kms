# DNS Made Easy API Client

- **Category:** dev
- **Status:** active
- **Owners:** Platform Engineering (LTS)

## Summary

A Python client for DNS Made Easy's REST API, used by the DCV Watchdog to create, read, and delete the `_dnsauth.<domain>` TXT records that close the DigiCert DCV loop.

## Behavior

- Auth: HMAC-SHA1 of the current HTTP date with the DME secret key, plus the API key, sent as three request headers (`x-dnsme-apiKey`, `x-dnsme-requestDate`, `x-dnsme-hmac`).
- Credentials fetched at cold start from the `atmsec` KV.
- Operations used by the Watchdog:
  - Look up the DME domain record.
  - List TXT records under a domain.
  - Create a TXT record at `_dnsauth.<domain>`.
  - Delete a TXT record after the cleanup window expires.

## Entry Points

- `domain-management/functions/dcv-watchdog/src/DcvWatchdogFunction/clients/dme_client.py`.
- Tests: `domain-management/functions/dcv-watchdog/tests/clients/`.

## Key Dependencies

- [Shared API Credentials (atmsec)](../business/shared-api-credentials.md).
- `requests` (HTTP).

## Related Features

- [DCV Watchdog Function](dcv-watchdog-function.md)
- [DCV State Machine](dcv-state-machine.md)

## Change Log

- 2026-04-21: Seeded.
