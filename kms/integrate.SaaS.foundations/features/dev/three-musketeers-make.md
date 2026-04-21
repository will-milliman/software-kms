# Three Musketeers Make

- **Category:** dev
- **Status:** active
- **Owners:** @milliman-lts/platform-engineering

## Summary

Build, test, lint, scan, and publish are driven through the **Three Musketeers** pattern: **Docker** + **docker-compose** + **Make**. This gives identical behavior on developer machines and CI agents, with no toolchain installed on the host beyond Docker and make.

## Behavior

- Root `Makefile` dispatches `tenant_admin__*`, `auditlog__*`, `auth_server__*` to the corresponding `src/make.mk`.
- Each service's `src/make.mk` includes the root `docker.mk`, which provides standard targets that call `docker compose` against `../docker-compose.yaml`:
  - `lint` → `checkov` against the Dockerfile.
  - `build` → compose build.
  - `scan_image` → `trivy image`.
  - `tag`, `publish`, `push` (gated on `AGENT_OS` — CI only).
  - `clean` → compose down + image rm.
- Env vars: `TAG` (defaults to `dev`), `DOCKER_BUILDKIT=1`, `DEFAULT_CONTAINER_REGISTRY=integratesolutions.azurecr.io`.
- `env-local.mk` (gitignored, copied from `env-local.template`) supplies `DEVOPS_PAT` for private feed auth.

## Entry Points

- `Makefile:10-17` — top-level dispatch.
- `docker.mk:1-47` — shared docker recipes.
- `env-local.template` — PAT placeholder.
- Per-service: `tenant-admin/src/make.mk`, `audit-log/src/make.mk`, `auth-server/src/make.mk`, `audit-log/src/Integrate.SaaS.AuditLog.Service/make.mk`, etc.

## Key Dependencies

- GNU Make.
- Docker + Docker Compose.
- `checkov`, `trivy` container images.

## Related Features

- [Docker Compose Test Harness](./docker-compose-test-harness.md)
- [Azure DevOps Pipelines](./azure-devops-pipelines.md)
- [Shared Pipeline Templates](./shared-pipeline-templates.md)

## Change Log

- 2026-04-21: Seeded.
