# Auth Server User Migration Job

- **Category:** dev
- **Status:** active
- **Owners:** @milliman-lts/platform-engineering

## Summary

Standalone `Integrate.SaaS.AuthServer.UserMigration` job that migrates auth-server user records outside the issuer startup path, with Kubernetes-job packaging, domain filtering, deduplication strategies, and explicit telemetry flushing for short-lived runs.

## Behavior

- Runs as a separate .NET worker project and container image.
- AppHost can include the job so local/dev distributed startup exercises the migration path.
- Migration handles malformed source/destination users defensively and skips invalid email/login domains instead of failing the whole run.
- Optional `TenantDomainFilter` restricts migrated users to one tenant domain.
- `DeduplicationStrategy` selects whether duplicate users keep the earliest or latest login.
- The worker flushes OpenTelemetry traces/logs during shutdown and the host grants a longer shutdown timeout to reduce telemetry loss.
- Kustomize overlay tooling generates per-run Kubernetes job overlays with strategy and tenant-domain environment settings.

## Entry Points

- `auth-server/src/Integrate.SaaS.AuthServer.UserMigration/` — worker project, Dockerfile, Kubernetes job, make targets, docs, and overlay skill.
- `auth-server/src/Integrate.SaaS.AuthServer/Data/UserStoreMigrationService.cs` — migration implementation.
- `auth-server/src/Integrate.SaaS.AuthServer/Data/DeduplicationStrategies/` — deduplication implementations.
- `auth-server/src/Integrate.SaaS.AuthServer/Extensions/TenantDomainExtensions.cs` — safe tenant-domain extraction.
- `auth-server/.pipelines/usermigration.build.yaml` — migration image build.

## Key Dependencies

- Azure Blob-backed auth user store.
- OpenTelemetry logging/tracing providers.
- Kubernetes Job deployment overlays.

## Related Features

- [OIDC Issuer](../business/oidc-issuer.md)
- [OpenTelemetry Observability](./opentelemetry-observability.md)

## Change Log

- 2026-05-07: PR #404 AB#91412 Extract User Migration code into a project that can be run as a k8s job — introduced the standalone migration project, Docker/Kubernetes packaging, pipeline, AppHost integration, and direct migration-service tests.
- 2026-05-07: PR #429 AB#91842 Enhance telemetry flushing and configure shutdown timeout — migration worker now explicitly flushes traces/logs before process exit and extends host shutdown timeout.
- 2026-05-07: PR #430 AB#91850 Fix user migration to skip over invalid users — invalid email/login domains now produce empty safe domains and are skipped/logged instead of throwing.
- 2026-05-07: PR #432 AB#83336 Implement user deduplication strategy and migration enhancements — added tenant-domain filtering, earliest/latest-login dedupe strategies, tests, docs, and overlay generation tooling.
