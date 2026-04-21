# Multi-Environment Spoke Provisioning

- **Category:** business
- **Status:** active (Incubating)
- **Owners:** Platform Engineering (LTS)

## Summary

A single `ssl-cert-spoke` Terraform stack ships to multiple environments (`apidev`, `dev`, `gitopsdev`, `gitopsprod`, and any new one a product team needs) via **config-only** changes — new env = new JSON file + new pipeline variable group.

## Behavior

- Per-env config files under `ssl-cert-spoke/configuration/`:
  - `apidev.json`, `dev.json`, `gitopsdev.json`, `gitopsprod.json`.
- A new env is added by dropping a new `<env>.json`, wiring a pipeline variable group, and running the infra-cd pipeline.
- The hub grants the new spoke's workload identity access to the hub KV via `domain-management/deploy/spoke_permissions.tf` (spoke IDs enumerated in hub config).

## Entry Points

- Spoke config dir: `ssl-cert-spoke/configuration/`.
- Spoke pipeline: `ssl-cert-spoke/.pipelines/infra-cd.yaml`.
- Hub spoke-permission grants: `domain-management/deploy/spoke_permissions.tf`.
- Hub config includes spoke IDs: `domain-management/configuration/{dev,prod}.json`.

## Key Dependencies

- [Hub-Spoke Cert Distribution](hub-spoke-cert-distribution.md).
- [Spoke Onboarding Runbook](spoke-onboarding-runbook.md).

## Related Features

- [Azure DevOps OIDC Pipelines](../dev/azure-devops-oidc-pipelines.md)

## Change Log

- 2026-04-21: Seeded.
