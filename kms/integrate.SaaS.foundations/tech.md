# Tech Stack

## Languages & runtimes

| Language / runtime | Where | Version |
|---|---|---|
| C# / .NET | `auth-server/`, `audit-log/` | **.NET 10** (SDK 10.0.100, `net10.0`), C# LangVersion 14 |
| C# / .NET | `tenant-admin/`, `reports/` | **.NET 9** (`net9.0`) |
| C# multi-target | `audit-log/src/Integrate.SaaS.AuditLog.Client` | `net10.0;net9.0;net6.0` (broad consumer compat) |
| TypeScript | `tenant-admin/src/ClientApp/` | `~5.7` |
| React | `tenant-admin/src/ClientApp/` | **19.2.4** (React Compiler enabled via `babel-plugin-react-compiler`) |
| Node | Build-time for ClientApp | `node:20-alpine` in Docker, pnpm `10.9.0` via corepack |
| PowerShell / Bash | `auth.ps1`, `auth.sh` | Milliman AAD `az login` helpers |
| Make | `Makefile`, `docker.mk`, per-service `make.mk` | GNU make |

## Frameworks

### Backend (.NET)

- **ASP.NET Core 10** (auth-server, audit-log) / **9** (tenant-admin, reports).
- **.NET Aspire** 13.0.2 / 13.1.0 for AppHosts; `Aspire.AppHost.Sdk`, `Aspire.Hosting.Azure.Storage`, `Aspire.Hosting.Keycloak` (preview).
- **Minimal APIs** in the audit-log Sidecar and WebHookRouter.
- **MVC controllers** in IssuerService, PortalService, tenant-admin API, auth-server Api.
- **Background services** (`BackgroundService`) — `AuditEventQueueService`, `AuthorizationCodeCleanupService`, `RefreshTokenCleanupService`, `TenantFixupService`.
- **YARP 2.3.0** (`Yarp.ReverseProxy`) + `Microsoft.Extensions.ServiceDiscovery.Yarp` — used in `auth-server/Ingress` (production gateway) and `tenant-admin/Ingress` (local dev host-routing simulator).

### Auth / Identity

- **Custom OIDC issuer** — hand-rolled (not IdentityServer / OpenIddict / Duende). See `auth-server/src/Integrate.SaaS.AuthServer/Services/AuthorizationService.cs`, `ProtocolResponseFactory.cs`, `Abstractions/`, `WellKnownEndpoints/`, `Logout/`.
- `Microsoft.AspNetCore.Authentication.OpenIdConnect` 10.0.2 — upstream federation (Issuer → Keycloak in dev; Auth0 / Entra ID in prod).
- `Microsoft.AspNetCore.Authentication.JwtBearer` 10.0.2 — resource-server token validation (Api, ConfigurationService, UpdateService, WebHookRouter).
- `Microsoft.Identity.Web` 3.14.1 / `Microsoft.Identity.Client` 4.77.0 — tenant-admin JWT + Azure AD.
- **Keycloak 26.3** (`quay.io/keycloak/keycloak:26.3`) — dev-only upstream IdP, realm imported from `.realms/Integrate-realm.json`.
- **Rainier.ServiceToolkit** 9.0.51 — internal platform library (JWT, config, error-format, well-known endpoints, validation).

### Frontend (tenant-admin ClientApp)

- **React 19** + **Vite 6** + `vite-tsconfig-paths`.
- **TanStack React Router 1.159** with auto code-splitting (`tenant-admin/src/ClientApp/vite.config.ts:63`). Routes: `index`, `app-download`, `assignment`, `groups`, `reports`, `roles`, `__root`.
- **Redux Toolkit 2.5** + **RTK Query** — `src/store/api.ts:17-33` (tag types `TenantDetail`, `TenantEnvironmentDetail`, `ReportDetail`).
- **IBM Carbon** design system — `@carbon/react 1.100.0`, `@carbon/icons-react 11.74.0`, `styled-components 6.3.9`.
- **Internal libs** — `@integrate-core-ui/{auth,frame,grid,modal,store,themes,formatter}` from the Azure DevOps npm feed.
- **Power BI** — `powerbi-client-react 2.0.2`, `powerbi-models 2.0.1`.
- **Testing** — Vitest 3 (90% coverage thresholds), MSW 2.7, Playwright 1.58, Testcontainers 10.24, `@testing-library/*`, Hono-based mock API in Docker.
- **Lint/format** — ESLint 9, Prettier 3 + `@trivago/prettier-plugin-sort-imports`, Husky 9.1.7, lint-staged.

### Other notable .NET NuGets

| Package | Purpose |
|---|---|
| `MediatR` 10.0.1 + `MediatR.Extensions.Microsoft.DependencyInjection` | CQRS in tenant-admin; `AuditBehavior<,>` pipeline |
| `Microsoft.FeatureManagement(.AspNetCore)` 4.3.0 | Feature flags; gates `Reporting` in tenant-admin |
| `Microsoft.PowerBI.Api` 4.22.0 + `Milliman.Hal` 8.0.82 | Embedded BI, HAL hypermedia |
| `Swashbuckle.AspNetCore(.Annotations)` 9.0.4 | Swagger (tenant-admin) |
| `OwaspHeaders.Core` 10.2.0 | `UseIntegrateSecurityHeaders()` everywhere in auth-server |
| `YamlDotNet` | Release-notes parsing in UpdateService |
| `Azure.Data.Tables` 12.11.0, `Azure.Storage.Blobs`, `Azure.Storage.Queues` 12.24.0 | Primary persistence |
| `Microsoft.Extensions.Http.Resilience` 10.0.0 | Standard resilience on all HttpClients |
| `Microsoft.Extensions.ServiceDiscovery` 10.0.0 | Aspire service discovery |
| `OpenTelemetry.*` 1.13.x + `Azure.Monitor.OpenTelemetry.AspNetCore` 1.3.0 | Observability |
| `xunit.v3` 3.2.x / `xunit` 2.9.3 | Tests |
| `FakeItEasy` 8.3.0, `Moq`, `FluentAssertions` (ignored by dependabot) | Test doubles / assertions |
| `Testcontainers` 4.x + `Testcontainers.Azurite` | Integration test storage |

## Infrastructure

- **Azure Storage** is the dominant persistence model (no relational DB anywhere in this repo):
  - **Blob Storage** — tenant/env/app JSON, auth-tenants, auth-clients, auth-users, auth-codes, auth-refresh, DPAPI keys, portal-sessions, sample-sessions, configuration-store, update-downloads, tenant-admin audit logs (append blobs).
  - **Table Storage** — `userindex` (auth-server), `auditlog` (audit-log service).
  - **Queue Storage** — `audit-events`, `audit-events-dl` (dead-letter).
- **Azurite** (`mcr.microsoft.com/azure-storage/azurite`) for local dev — managed by Aspire AppHosts with persistent volumes (`tenant-admin`, `issuer`).
- **Azure Container Registry** — `integratesolutions.azurecr.io` (base images + pushed service images).
- **Azure DevOps NuGet feed** — `milliman` feed on `dev.azure.com/mgalfadev`; requires `DEVOPS_PAT`.
- **Azure DevOps npm feed** — `@integrate-core-ui/*` scope.
- **Azure Monitor** — OpenTelemetry exporter for production telemetry.
- **Azure AD tenant** — `da280eb2-7328-4fc8-9521-154de96d70eb` (Milliman); target audience `mg-alfa.milliman.com` (tenant-admin `Constants.cs:54`).

## Build tooling & CI

### Make orchestration

- Root `Makefile:10-17` — three pattern rules forward to per-service `make.mk`:
  - `tenant_admin__% → tenant-admin/src`
  - `auditlog__% → audit-log/src`
  - `auth_server__% → auth-server/src`
  - (`reports` has no entry — it is a pure NuGet library, not a container.)
- Root `docker.mk:1-47` — shared `lint` (checkov), `build`, `scan_image` (trivy), `tag`, `publish`, `push` (guarded by `AGENT_OS`), `clean`. Uses `docker compose -f ../docker-compose.yaml`. `TAG?=dev`, `DEFAULT_CONTAINER_REGISTRY=integratesolutions.azurecr.io`.
- Per-service `src/docker-compose.yaml` — test services (`*_test`), image-build services, `checkov`, `trivy`, secret `FEED_ACCESSTOKEN` from `DEVOPS_PAT`.

### Azure DevOps pipelines

- **Shared templates** at `.pipelines/templates/`:
  - `common-variables.yaml` — `BuildNumber = 1.0.$(counter)`, `VersionSuffix`, `pushImages` (true on `main` or `forcePush`).
  - `build-docker-image.yaml` — AzureCLI ACR login → `make <prefix>__lint` → `make <prefix>__build` → `make <prefix>__scan_image` → (conditional) `make <prefix>__publish`.
- **Per-service pipelines**:
  - `auth-server/.pipelines/*.build.yaml` — 6 pipelines (api, issuerservice, portalservice, configurationservice, updateservice, webhookrouterservice).
  - `audit-log/src/.pipelines/{client,service,sidecar}.build.yaml` — client packages & pushes NuGet; service & sidecar build docker images.
  - `tenant-admin/.pipelines/{tenantadmin.api,tenantadmin.web}.build.yaml` — API docker; Web runs pnpm build + Playwright E2E then docker.
  - `reports/.pipelines/reports.build.yaml` — dotnet restore/build/test/pack/push (no docker).
- **Weekly Tuesday scheduled rebuilds** refresh base images.

### No GitHub Actions

- `.github/` contains only `CODEOWNERS` and `dependabot.yml`. No `.github/workflows/`.

## Versioning

All service images and NuGet packages use `BuildNumber = 1.0.$(counter)` from `common-variables.yaml:8-13`; branches other than `main` get `VersionSuffix = -<cleanedBranchName>`.

## Security tooling

- **Checkov** — IaC/Dockerfile scanning (`docker.mk:11-15`).
- **Trivy** — image vulnerability scan (`docker.mk:23-27`).
- **OwaspHeaders.Core** — runtime security headers on every auth-server service.
- **mkcert** — dev token-signing + DPAPI certs under `.token-certs/` and `.dpapi-certs/`.
- **Data Protection** — X.509 cert + blob-backed key ring (`auth-dpapi`, `portal-dpapi`, `sample-dpapi` containers).
