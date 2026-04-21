# Cross-repo Makefile Utilities

- **Category:** dev
- **Status:** active
- **Owners:** devex

## Summary

Shared GNU Make fragments under `shared/make/` implementing the "Three Musketeers" pattern (Make + Docker + Compose), reused by multiple Milliman repos to standardize container-based builds and developer workflows.

## Behavior

- Includes targets for dotnet build/test, docker image build, image scanning, publish, and clean.
- Works identically on engineer laptops and in CI by delegating heavy steps to containers.
- Consuming repos `include` these `.mk` files from a submodule or vendored copy.

## Entry Points

- `shared/make/*.mk` (e.g. `dotnet.mk`, `docker_image.mk`, `compose.mk`).

## Key Dependencies

- [Trivy + Checkov Image Scanning](image-scanning.md).
- [Azurite Emulator for Tests](azurite-for-tests.md).

## Related Features

- [Shared Pipeline Templates](shared-pipeline-templates.md)

## Change Log

- 2026-04-21: Seeded.
