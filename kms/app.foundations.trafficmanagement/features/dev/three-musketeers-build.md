# Three Musketeers Build

- **Category:** dev
- **Status:** active
- **Owners:** Platform Engineering (LTS)

## Summary

The repo follows the **Three Musketeers** pattern — `make → docker compose → container`. Nothing (`terraform`, `python`, `pytest`, `black`, `ruff`, `checkov`, `conftest`, `tflint`) is installed on the host. Instead, every tool runs inside a pinned container image pulled from `integratesolutions.azurecr.io`.

## Behavior

- Developer runs e.g. `make domain__infra__plan ENV=dev`.
- The Makefile invokes `docker compose run` against `docker-compose.yaml`, which mounts the repo tree into the tool container.
- The tool container is fixed-version (e.g. `terraform:1.14.5`, `checkov:3`) so CI and local produce identical output.
- Auth passes through via `auth.sh` (interactive `az login` + ADO PAT) which writes credentials into a docker volume shared by subsequent `make` calls.

## Entry Points

- Root `Makefile`.
- `docker-compose.yaml` (root).
- `auth.sh`, `logout.sh`.
- `shared/terraform.mk`.
- Per-stack `make.mk`.
- Submodule `cicd-shared/make/help.mk` (self-documenting help).

## Key Dependencies

- Docker + docker compose.
- Access to `integratesolutions.azurecr.io`.

## Related Features

- [Terraform Four-Stack Layout](terraform-four-stack-layout.md)
- [OPA/Conftest + Checkov Gates](opa-conftest-checkov-gates.md)

## Change Log

- 2026-04-21: Seeded.
