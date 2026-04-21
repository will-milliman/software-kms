# DigiCert v2 API Client

- **Category:** dev
- **Status:** active
- **Owners:** Platform Engineering (LTS)

## Summary

A thin Python client over DigiCert's CertCentral v2 REST API used by the DCV Watchdog. Handles auth, domain lookups, DCV submission, and status polling.

## Behavior

- Base URL: DigiCert CertCentral v2.
- Auth: `X-DC-DEVKEY: <api_key>` header — key fetched from the `atmsec` KV at function cold start.
- Calls used by the Watchdog:
  - Look up a domain.
  - Add a domain.
  - Activate a domain.
  - Submit DCV (method = `dns-txt-token`).
  - Poll validation status.
- Timeouts + retries wrap `requests` for transient failures.

## Entry Points

- `domain-management/functions/dcv-watchdog/src/DcvWatchdogFunction/clients/digicert_client_v2.py`.
- Tests (stubs via `responses`/`requests-mock`): `domain-management/functions/dcv-watchdog/tests/clients/`.

## Key Dependencies

- [Shared API Credentials (atmsec)](../business/shared-api-credentials.md).
- `requests` (HTTP).

## Related Features

- [DCV Watchdog Function](dcv-watchdog-function.md)
- [DCV State Machine](dcv-state-machine.md)

## Change Log

- 2026-04-21: Seeded.
