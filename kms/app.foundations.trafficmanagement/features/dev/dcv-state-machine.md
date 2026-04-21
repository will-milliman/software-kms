# DCV State Machine

- **Category:** dev
- **Status:** active
- **Owners:** Platform Engineering (LTS)

## Summary

The heart of the DCV Watchdog. A finite state machine where `DomainState × DomainAction` is a static lookup table, so each run is deterministic: compute state → look up action → dispatch to handler.

## Behavior

- **States** (`DomainState` enum):
  - `NOT_EXISTS`
  - `EXISTS_INACTIVE`
  - `EXISTS_ACTIVE_NOT_VALIDATED`
  - `EXISTS_ACTIVE_PENDING_VALIDATION`
  - `EXISTS_ACTIVE_VALIDATED_OK`
  - `EXISTS_ACTIVE_VALIDATED_EXPIRING`
- **Actions** (`DomainAction` enum):
  - `NO_ACTION`
  - `ADD_DOMAIN`
  - `ACTIVATE_DOMAIN`
  - `SUBMIT_FOR_VALIDATION`
  - `RESUBMIT_PENDING_TIMEOUT`
  - `CLEANUP_DCV_RECORDS`
- Each run:
  1. Handler enumerates managed domains.
  2. `DomainState` computed from DigiCert + KV state + timestamps.
  3. Matrix lookup yields the action.
  4. Action module is invoked via a function-factory registry.
  5. OTEL spans recorded at handler + action level.

## Entry Points

- State machine: `domain-management/functions/dcv-watchdog/src/DcvWatchdogFunction/domain_state.py`.
- Action registry + dispatch: `DcvWatchdogFunction/actions/__init__.py`.
- Action modules: `DcvWatchdogFunction/actions/{add_domain,activate_domain,submit_validation,cleanup_dcv_records}.py`.
- Tests: `domain-management/functions/dcv-watchdog/tests/`.

## Key Dependencies

- [DCV Watchdog Function](dcv-watchdog-function.md).
- [DigiCert v2 API Client](digicert-v2-client.md).
- [DNS Made Easy API Client](dns-made-easy-client.md).

## Related Features

- [business DCV Automation](../business/dcv-automation.md)

## Change Log

- 2026-04-21: Seeded.
