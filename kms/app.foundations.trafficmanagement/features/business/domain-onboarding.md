# Domain Onboarding

- **Category:** business
- **Status:** active (Incubating)
- **Owners:** Platform Engineering (LTS)

## Summary

Adding a new public domain to Milliman LTS's managed-cert program is a Terraform-only change: declare the domain in the hub config, plan/apply, and the DCV Watchdog plus DigiCert handle the rest.

## Behavior

- A domain entry in `domain-management/configuration/<env>.json` produces:
  - A DigiCert domain registration.
  - A DME domain record.
  - A KV certificate resource (OV, auto-renew).
- First Watchdog run after apply transitions the domain through `NOT_EXISTS → EXISTS_INACTIVE → EXISTS_ACTIVE_NOT_VALIDATED → EXISTS_ACTIVE_PENDING_VALIDATION → EXISTS_ACTIVE_VALIDATED_OK` (see [DCV State Machine](../dev/dcv-state-machine.md)).
- No human interaction with DigiCert or DME portals is needed.

## Entry Points

- Hub env config: `domain-management/configuration/dev.json`, `configuration/prod.json`.
- Per-domain submodule: `domain-management/deploy/modules/domain/{main,variables,outputs}.tf`.
- Watchdog trigger: `domain-management/functions/dcv-watchdog/` (daily).

## Key Dependencies

- [SSL Certificate Issuance & Renewal](ssl-certificate-issuance-and-renewal.md).
- [DCV Automation](dcv-automation.md).
- DNS Made Easy (external).

## Related Features

- [Spoke Onboarding Runbook](spoke-onboarding-runbook.md)
- [Multi-Environment Spoke Provisioning](multi-env-spoke-provisioning.md)

## Change Log

- 2026-04-21: Seeded.
