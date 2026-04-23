# Containerised Toolchain

- **Category:** dev
- **Status:** active
- **Owners:** Platform Engineering

## Summary

`docker-compose.yaml` declares a pinned set of tooling containers pulled from the internal `integratesolutions.azurecr.io` registry, so Terraform, lint, docs, policy, and scanning commands run the same way on a developer laptop and in CI.

## Behavior

Services defined:

| Service | Image | Purpose |
| --- | --- | --- |
| `terraform` | `integrate/tooling/terraform:1.14.5` | Runs Terraform CLI against the stack. Consumes ARM / AZDO / DME env vars. |
| `terraform_lint` | `ghcr.io/terraform-linters/tflint` | `tflint` runner. |
| `terraform_docs` | `quay.io/terraform-docs/terraform-docs:0.20.0` | Generates module docs. |
| `checkov` | `integrate/tooling/checkov:3` | IaC security scan; feeds [Checkov Plan Scanning](checkov-scanning.md). |
| `conftest` | `docker.io/openpolicyagent/conftest:v0.62.0` | OPA/Rego policy check on plans. |
| `terraform-change-annotate` | `integrate/tooling/terraform-change-annotate:latest` | Posts a change summary to PRs; consumes ADO + GitHub App creds. |
| `utils` | `integrate/tooling/alpine-zip:1.0` | Generic zip/alpine helpers. |
| `bash` | `docker.io/library/bash:latest` | Ad-hoc shell. |
| `python` | `docker.io/library/python:3.12` | Python scripting. |
| `black` | `docker.io/pyfound/black:latest` | Python formatting. |

Cross-cutting patterns:

- `userns_mode: host` and explicit `user: ${USER_ID}:${GROUP_ID}` on most services to keep file ownership correct on bind mounts.
- Repo root is bind-mounted as `/app` (some services `:rw`).
- `~/.azure` is bind-mounted into the `terraform` service to reuse the developer's Azure CLI context.
- `terraform-change-annotate` is wired with ADO pipeline variables and GitHub App creds (`GITHUB_APP_ID=2477755`, `GITHUB_APP_INSTALLATION_ID=99737301`).

## Entry Points

- `docker-compose.yaml:1` — full service list.
- `docker-compose.yaml:5` — Terraform image pin (must match `required_version` in `provider.tf:2`).
- `env-local.mk.tpl` — sets `USE_COMPOSE=true` to route shared make targets through compose.

## Key Dependencies

- Docker / docker-compose on the host.
- `integratesolutions.azurecr.io` — registry access; developers authenticate via `auth.sh` (`az acr login`).
- [cicd-shared Submodule](cicd-shared-submodule.md) — `terraform.mk` is what knows how to invoke compose services.

## Related Features

- [Local Developer Auth](local-dev-auth.md)
- [Checkov Plan Scanning](checkov-scanning.md)
- [Conftest Policy Harness](conftest-policy.md)

## Change Log

- 2026-04-21: Seeded.
