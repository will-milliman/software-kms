# Trivy + Checkov Image Scanning

- **Category:** dev
- **Status:** active
- **Owners:** devex / security

## Summary

Shared Make target `shared/make/docker_image.mk` runs Trivy (CVE scan) and Checkov (IaC misconfiguration scan) against every docker image built, failing the build on policy violations.

## Behavior

- Invoked automatically after `docker build` via the Make target, so both local and CI builds get the same gate.
- Severity thresholds are configurable; `HIGH` and `CRITICAL` fail by default.
- Results are uploaded as pipeline artifacts.

## Entry Points

- `shared/make/docker_image.mk`.
- Consumers via [Shared Pipeline Templates](shared-pipeline-templates.md).

## Key Dependencies

- Trivy, Checkov (pulled as container tools).

## Related Features

- [Cross-repo Makefile Utilities](cross-repo-makefiles.md)

## Change Log

- 2026-04-21: Seeded.
