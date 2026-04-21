# OPA/Conftest + Checkov Gates

- **Category:** dev
- **Status:** active
- **Owners:** Platform Engineering (LTS)

## Summary

Every Terraform plan is gated by two policy scanners: **Checkov** (opinionated built-in rules for Azure) and **Conftest** running custom **OPA/Rego** policies at `shared/policy/`. Failures break the pipeline.

## Behavior

- Invoked by the shared `check_compliant` make target (`shared/terraform.mk`).
- Checkov 3 runs against the Terraform source.
- Conftest runs against `terraform plan -json` output using policies from `shared/policy/`.
- Custom policies enforce things like: mandatory tag set (see [Mandatory Tagging Policy](mandatory-tagging-policy.md)), required RBAC auth on KV, required diagnostics.

## Entry Points

- `shared/terraform.mk` — orchestration target `check_compliant`.
- `shared/policy/` — Rego rules.
- `docker-compose.yaml` — pinned `checkov:3` + `conftest` images from `integratesolutions.azurecr.io`.
- Validate pipeline template: `shared/.pipelines/templates/validate-iac-template.yaml`.

## Key Dependencies

- [Three Musketeers Build](three-musketeers-build.md).

## Related Features

- [Mandatory Tagging Policy](mandatory-tagging-policy.md)
- [Azure DevOps OIDC Pipelines](azure-devops-oidc-pipelines.md)

## Change Log

- 2026-04-21: Seeded.
