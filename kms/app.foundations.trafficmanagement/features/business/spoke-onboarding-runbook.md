# Spoke Onboarding Runbook

- **Category:** business
- **Status:** active (Incubating)
- **Owners:** Platform Engineering (LTS)

## Summary

A documented, step-by-step procedure for registering a new spoke subscription with the certificate-distribution hub. Lives next to the stack it describes so it stays in sync with the Terraform.

## Behavior

- The runbook walks an operator through:
  1. Creating the spoke workload identity.
  2. Adding it to the hub's `spoke_permissions` list.
  3. Adding a spoke env config (`ssl-cert-spoke/configuration/<env>.json`).
  4. Running `domain__infra__plan` / `domain__infra__deploy` and `spoke__infra__plan` / `spoke__infra__deploy`.
  5. Verifying the Event Grid subscription + first cert sync.

## Entry Points

- Runbook file: `domain-management/docs/runbooks/adding-spoke-for-cert-distribution.md`.

## Key Dependencies

- [Hub-Spoke Cert Distribution](hub-spoke-cert-distribution.md).
- [Multi-Environment Spoke Provisioning](multi-env-spoke-provisioning.md).

## Related Features

- [Azure DevOps OIDC Pipelines](../dev/azure-devops-oidc-pipelines.md)

## Change Log

- 2026-04-21: Seeded.
