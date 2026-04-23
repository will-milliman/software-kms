# Conftest Policy Harness

- **Category:** dev
- **Status:** planned (wired but empty)
- **Owners:** Platform Engineering

## Summary

A Conftest / OPA-Rego scaffold is in place for plan-level assertions, but no actual rules are written yet. The `conftest` compose service is available and a policy package stub lives alongside the ingress stack.

## Behavior

- `ingress/deploy/tests/checks.rego` declares `package main` and imports `input as tfplan` (`ingress/deploy/tests/checks.rego:1`). There are no rules defined.
- `conftest` compose service pinned to `v0.62.0` (`docker-compose.yaml:41`).
- There is no pipeline step that invokes Conftest today; integration is expected once rules are written (likely through a shared `terraform.mk` target).

## Entry Points

- `ingress/deploy/tests/checks.rego:1`
- `docker-compose.yaml:41`

## Key Dependencies

- Open Policy Agent / Conftest.
- [Containerised Toolchain](containerised-toolchain.md).

## Related Features

- [Checkov Plan Scanning](checkov-scanning.md)
- [Reusable ADO IaC Templates](reusable-azdo-templates.md)

## Change Log

- 2026-04-21: Seeded.
