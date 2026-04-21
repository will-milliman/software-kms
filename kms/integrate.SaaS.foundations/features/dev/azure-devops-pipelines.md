# Azure DevOps Pipelines

- **Category:** dev
- **Status:** active
- **Owners:** @milliman-lts/platform-engineering

## Summary

CI/CD runs on **Azure DevOps** (`dev.azure.com/mgalfadev/Rainier`). The root `README.md` table lists 13 build definitions — one per deployable artifact — each triggering on the relevant path in the monorepo, with a **weekly Tuesday schedule** to refresh base images.

## Behavior

- Per-pipeline path filter scopes triggers so changes to e.g. `audit-log/**` don't rebuild tenant-admin.
- Builds run `make <prefix>__test` via docker compose.
- Versioning centralized via the shared `common-variables.yaml` (`BuildNumber = 1.0.$(counter)` with `VersionSuffix`).
- Publishing gated: images push to ACR only on `main` (or `forcePush=true`); NuGet packages push to the `milliman` AzDO feed on `main`.

## Entry Points

- `auth-server/.pipelines/*.build.yaml` — 6 pipelines (api, issuerservice, portalservice, configurationservice, updateservice, webhookrouterservice).
- `audit-log/src/.pipelines/{client,service,sidecar}.build.yaml`.
- `tenant-admin/.pipelines/tenantadmin.api.build.yaml` (75 lines).
- `tenant-admin/.pipelines/tenantadmin.web.build.yaml` (131 lines — Playwright E2E included).
- `reports/.pipelines/reports.build.yaml` (64 lines — dotnet pack + push).
- Root `README.md:5-16` — badge table listing all 13 build definitions.

## Key Dependencies

- Shared templates at `.pipelines/templates/`.
- Three Musketeers make pattern.
- Private AzDO NuGet / npm feeds.

## Related Features

- [Shared Pipeline Templates](./shared-pipeline-templates.md)
- [Three Musketeers Make](./three-musketeers-make.md)
- [Dependabot Grouped Updates](./dependabot-grouped-updates.md)

## Change Log

- 2026-04-21: Seeded.
