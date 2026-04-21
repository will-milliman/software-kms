# Terraform Four-Stack Layout

- **Category:** dev
- **Status:** active
- **Owners:** Platform Engineering (LTS)

## Summary

The repo is split into four independently-deployable Terraform stacks, each with its own `deploy/`, `configuration/<env>.json`, `make.mk`, and `.pipelines/`. Shared behavior lives in `shared/modules/` and `shared/terraform.mk`.

## Behavior

- Stacks:
  - `atmsec/` — shared secrets (GA).
  - `domain-management/` — the hub (Incubating).
  - `ssl-cert-spoke/` — the spokes (Incubating).
  - `global-app-ingress/` — Azure Front Door (Incubating).
- Each stack declares its own `provider.tf`, `context.tf`, `tags.tf`, `rg.tf`, and an `azurerm` backend.
- Stacks deploy in any order but have logical dependencies enforced by role grants (atmsec before hub, hub before spokes).
- Each stack is invoked through the root `Makefile` by prefix (`atmsec__`, `domain__`, `spoke__`, `gai__`).

## Entry Points

- Root `Makefile`.
- Per-stack `make.mk`.
- Shared module root: `shared/modules/KeyVault/`, `shared/modules/flex-function-app/`.
- Shared terraform targets: `shared/terraform.mk`.

## Key Dependencies

- [Three Musketeers Build](three-musketeers-build.md).
- [Shared KeyVault Module](shared-keyvault-module.md).
- [Shared Flex Function-App Module](shared-flex-function-module.md).

## Related Features

- [Azure DevOps OIDC Pipelines](azure-devops-oidc-pipelines.md)
- [OPA/Conftest + Checkov Gates](opa-conftest-checkov-gates.md)

## Change Log

- 2026-04-21: Seeded.
