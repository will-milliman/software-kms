# Azure DevOps OIDC Pipelines

- **Category:** dev
- **Status:** active
- **Owners:** Platform Engineering (LTS)

## Summary

Every stack ships with one `infra-cd` pipeline and (where applicable) one `function-cd` pipeline. All deploys authenticate to Azure via **OIDC federated identity** (`ARM_USE_OIDC=true`) — there are no long-lived service-principal secrets stored in ADO.

## Behavior

- Stack pipelines:
  - `atmsec/.pipelines/infra-cd.yaml`.
  - `domain-management/.pipelines/infra-cd.yaml`, `function-cd.yaml`.
  - `ssl-cert-spoke/.pipelines/infra-cd.yaml`, `function-cd.yaml`.
  - `global-app-ingress/.pipelines/infra-deploy.yaml`.
- Shared templates: `shared/.pipelines/templates/{validate,package,deploy,destroy}-iac-template.yaml`.
- Validate stage runs `format_check → validate_terraform → lint_terraform → plan → plan_json → check_compliant` (Checkov + Conftest).
- Function pipelines: ruff + black + pytest → zip → upload to blob → trigger function restart.
- The `cicd-shared` submodule provides a self-documenting `make help` target (`cicd-shared/make/help.mk`).

## Entry Points

- Shared templates: `shared/.pipelines/templates/`.
- Per-stack `.pipelines/`.
- Submodule: `cicd-shared` (`.gitmodules`).

## Key Dependencies

- [Three Musketeers Build](three-musketeers-build.md).
- [OPA/Conftest + Checkov Gates](opa-conftest-checkov-gates.md).
- [Mandatory Tagging Policy](mandatory-tagging-policy.md).

## Related Features

- [Terraform Four-Stack Layout](terraform-four-stack-layout.md)
- [Multi-Environment Spoke Provisioning](../business/multi-env-spoke-provisioning.md)

## Change Log

- 2026-04-21: Seeded.
