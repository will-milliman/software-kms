# Azurite Emulator for Tests

- **Category:** dev
- **Status:** active
- **Owners:** cloud / devex

## Summary

Make targets and compose files bring up the Azurite Azure Storage emulator so integration tests that exercise Rainier.Origin, blob upload/download, and cloud-run-storage code paths can run locally and in CI without real Azure credentials.

## Behavior

- `make azurite-up` / `azurite-down` targets manage the container lifecycle.
- Tests configure connection strings to point at the emulator endpoint.
- Same setup is reused for the cloud-services pipelines.

## Entry Points

- `shared/make/azurite.mk` (approximate).
- `monorepo/cloud/` compose fragments.

## Key Dependencies

- Azurite container image.

## Related Features

- [Cloud Run Storage (Origin)](../business/cloud-run-storage.md)
- [Cross-repo Makefile Utilities](cross-repo-makefiles.md)

## Change Log

- 2026-04-21: Seeded.
