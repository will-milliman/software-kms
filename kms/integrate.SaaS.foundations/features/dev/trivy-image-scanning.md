# Trivy Image Scanning

- **Category:** dev
- **Status:** active
- **Owners:** @milliman-lts/platform-engineering

## Summary

Every container image produced by this repo is scanned with **Aqua Trivy** for OS-package and language-package CVEs before it is tagged and pushed to ACR.

## Behavior

- `make <prefix>__scan_image` invokes `docker compose run --rm trivy image "${IMAGE_NAME}:dev"`.
- Trivy runs as a compose service pulled from `integratesolutions.azurecr.io`.
- CI runs this step right after `build`, before `publish` (`.pipelines/templates/build-docker-image.yaml` step 3).

## Entry Points

- `docker.mk:23-27` — `scan_image` target.
- `auth-server/src/docker-compose.yaml`, `audit-log/src/docker-compose.yaml`, `tenant-admin/src/docker-compose.yaml` — `trivy` service definitions.
- `.pipelines/templates/build-docker-image.yaml` — consumer.

## Key Dependencies

- Trivy container image.

## Related Features

- [Three Musketeers Make](./three-musketeers-make.md)
- [Checkov Dockerfile Scanning](./checkov-dockerfile-scanning.md)

## Change Log

- 2026-04-21: Seeded.
