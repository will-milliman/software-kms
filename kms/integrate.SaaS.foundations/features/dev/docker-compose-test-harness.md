# Docker Compose Test Harness

- **Category:** dev
- **Status:** active
- **Owners:** @milliman-lts/platform-engineering

## Summary

Each sub-service has a `src/docker-compose.yaml` that defines containerised services for testing (`*_test`), image-building, and security scanning (`checkov`, `trivy`). The compose file is the single execution surface for `docker.mk` recipes.

## Behavior

- `*_test` services run `dotnet test --logger:trx`, mounting a shared network with Azurite.
- Build services produce the production images that later get tagged and pushed by CI.
- Secret `FEED_ACCESSTOKEN` is sourced from the host env var `DEVOPS_PAT` so builds can restore from the private Azure DevOps NuGet feed.
- Custom bridge networks (`saas_foundations` in audit-log) keep containers isolated.

## Entry Points

- `auth-server/src/docker-compose.yaml` — `authserver_test`, `client_test`, `api_test`, `portal_test`, `webhookrouter_test`, `config_test`, `update_test`, `issuer_test`, image builds, checkov, trivy.
- `audit-log/src/docker-compose.yaml` — `integration_test`, `sidecar_test`, `service_test`, `auditlog_service`, `auditlog_sidecar`, `dotnet` shared SDK container.
- `tenant-admin/src/docker-compose.yaml` — `unittest`, `tenantadmin_web`, `tenantadmin_api`, `checkov`, `trivy`.

## Key Dependencies

- Docker Compose.
- Azurite image.
- `checkov`, `trivy` images pulled from `integratesolutions.azurecr.io`.

## Related Features

- [Three Musketeers Make](./three-musketeers-make.md)
- [Checkov Dockerfile Scanning](./checkov-dockerfile-scanning.md)
- [Trivy Image Scanning](./trivy-image-scanning.md)

## Change Log

- 2026-04-21: Seeded.
