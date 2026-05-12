# DCV Watchdog Function

- **Category:** dev
- **Status:** active (Incubating)
- **Owners:** Platform Engineering (LTS)
- **Straddles:** business + dev — see [business DCV Automation](../business/dcv-automation.md).

## Summary

A Python 3.12 Azure Function, timer-triggered daily, that drives every managed domain through a DCV state machine and closes the DigiCert DNS-TXT DCV loop automatically. Deployed via `shared/modules/flex-function-app` on Flex Consumption (FC1).

## Behavior

- Entry: `DcvWatchdogFunction/main.py` resolves to `DcvWatchdogHandler.execute()` (`handler.py`).
- Timer trigger CRON: `0 0 * * *` (daily; `function.json`).
- On each run:
  1. Lists managed domains (hub config).
  2. For each, queries DigiCert + KV to compute current `DomainState`.
  3. Looks up `DomainAction` in the state-machine matrix (`domain_state.py`).
  4. Dispatches to one of `actions/{add_domain,activate_domain,submit_validation,cleanup_dcv_records}.py` (function-factory pattern).
  5. Emits OTEL spans via the `@traced` decorator.
- Emits structured `dcv_domain_status` log records for dashboards and force-flushes OTEL exporters before the timer function exits.
- Cleanup marks Terraform-managed DCV token/timestamp secrets as `DONE` instead of deleting or blanking them, avoiding Terraform drift while suppressing repeat cleanup.
- Configurable knobs (env vars):
  - `DCV_EXPIRY_THRESHOLD_DAYS` (default 30)
  - `PENDING_VALIDATION_TIMEOUT_HOURS` (default 24)
  - `CLEANUP_TXT_PERIOD_HOURS` (default 48)
  - `DRY_RUN` (default `true`)
- Has a comprehensive pytest suite.

## Entry Points

- Function source: `domain-management/functions/dcv-watchdog/src/`.
- Entry: `DcvWatchdogFunction/main.py`, `function.json`.
- Handler: `DcvWatchdogFunction/handler.py`.
- State machine: `DcvWatchdogFunction/domain_state.py`.
- Actions: `DcvWatchdogFunction/actions/*.py`.
- Clients: `DcvWatchdogFunction/clients/{digicert_client_v2,dme_client,keyvault_client}.py`.
- Instrumentation: `DcvWatchdogFunction/instrumentation/__init__.py`.
- Terraform: `domain-management/deploy/dcv_watchdog_func.tf`.
- Docs: `domain-management/functions/dcv-watchdog/README.md`, `QUICKREF.md`.
- Deploy pipeline: `domain-management/.pipelines/function-cd.yaml`.

## Key Dependencies

- [DCV State Machine](dcv-state-machine.md).
- [DigiCert v2 API Client](digicert-v2-client.md).
- [DNS Made Easy API Client](dns-made-easy-client.md).
- [Shared Flex Function-App Module](shared-flex-function-module.md).
- [OpenTelemetry Instrumentation](otel-instrumentation.md).

## Related Features

- [business DCV Automation](../business/dcv-automation.md)

## Change Log

- 2026-04-21: Seeded.
- 2026-05-12: PR #72 chore(AB#91000): force flush the trace buffer — DCV cleanup now writes `DONE` sentinels, emits domain status telemetry, and force-flushes OTEL providers before exit.
