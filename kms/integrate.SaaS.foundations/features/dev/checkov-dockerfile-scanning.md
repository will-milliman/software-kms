# Checkov Dockerfile Scanning

- **Category:** dev
- **Status:** active
- **Owners:** @milliman-lts/platform-engineering

## Summary

Every Dockerfile is statically scanned with **Bridgecrew Checkov** before each image build to catch insecure base images, missing `USER`, exposed secrets, etc.

## Behavior

- `make <prefix>__lint` invokes `docker compose run --rm checkov --file ${DOCKER_FILE} --framework dockerfile`.
- `checkov` is pulled as a compose service from the internal ACR.
- CI calls this via `build-docker-image.yaml` step 2 before `build`.

## Entry Points

- `docker.mk:11-15` — `lint` target.
- `auth-server/src/docker-compose.yaml`, `audit-log/src/docker-compose.yaml`, `tenant-admin/src/docker-compose.yaml` — `checkov` service definitions.
- `.pipelines/templates/build-docker-image.yaml` — consumer.

## Key Dependencies

- Checkov container image.

## Related Features

- [Three Musketeers Make](./three-musketeers-make.md)
- [Trivy Image Scanning](./trivy-image-scanning.md)

## Change Log

- 2026-04-21: Seeded.
