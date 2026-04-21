# Mandatory Tagging Policy

- **Category:** dev
- **Status:** active
- **Owners:** Platform Engineering (LTS)

## Summary

Every resource in every stack carries a standardized set of eight platform tags defined by `tags.tf` in each stack root. Conftest policies assert these tags are present and non-empty before a plan is allowed to apply.

## Behavior

- Required tag keys:
  - `tagging-version`
  - `system-name`
  - `system-owner`
  - `system-domain`
  - `environment-name`
  - `environment-owner`
  - `environment-cost-center`
  - `environment-data-classification`
- Values come from per-env `configuration/<env>.json` and the stack's `context.tf` (which composes local values into a single `tags` map).
- Each module in the stack reads `var.tags` (or the computed local) and applies it.
- The Conftest policies in `shared/policy/` reject any resource missing any of the eight.

## Entry Points

- Per-stack: `atmsec/deploy/tags.tf`, `domain-management/deploy/tags.tf`, `ssl-cert-spoke/deploy/tags.tf`, `global-app-ingress/deploy/tags.tf`.
- Per-stack: `context.tf` (tag composition).
- Policy: `shared/policy/`.

## Key Dependencies

- [OPA/Conftest + Checkov Gates](opa-conftest-checkov-gates.md).

## Related Features

- [Terraform Four-Stack Layout](terraform-four-stack-layout.md)

## Change Log

- 2026-04-21: Seeded.
