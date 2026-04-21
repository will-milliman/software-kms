# Architecture

## Component diagram (prose)

```
                  ┌──────────────────────────────────────────────────────────┐
                  │               End users (MG-ALFA / Integrate)            │
                  └─────────────────────────┬────────────────────────────────┘
                                            │ HTTPS
                                            ▼
                            ┌───────────────────────────────┐
                            │   YARP Ingress (auth-server)   │  auth-server/src/Integrate.SaaS.AuthServer.Ingress
                            │  path-based routing, header-   │  appsettings.json:10-128
                            │  gated webhook                 │
                            └───┬────────┬─────────┬─────────┬──┬───────────┘
                                │        │         │         │  │
                    ┌───────────▼┐  ┌────▼─────┐ ┌─▼─────┐ ┌─▼──▼──────┐ ┌──────────────┐
                    │ IssuerSvc  │  │ Api/User │ │Portal │ │Config/Upd │ │ WebHookRouter│
                    │ (OIDC)     │  │ Service  │ │Service│ │Update svc │ │  (EventGrid) │
                    └──────┬─────┘  └────┬─────┘ └───┬───┘ └──────┬────┘ └──────┬───────┘
                           │ upstream OIDC                                     │
                           ▼                                                   ▼
                 ┌────────────────────┐                             ┌────────────────────┐
                 │ Keycloak 26 (dev)  │                             │  Downstream systems │
                 │ Auth0/Entra (prod) │                             │  (tenant hooks)     │
                 └────────────────────┘                             └────────────────────┘

         ─────── Sibling services (parallel monorepo folders) ───────

   ┌────────────────────────┐         ┌────────────────────────┐         ┌────────────────────┐
   │ tenant-admin (Api+SPA) │─────┐   │ audit-log (Client SDK) │         │ reports (NuGet lib)│
   │  YARP Ingress local    │     │   │  ↓ HTTP                 │         │  IEmbeddedReport   │
   │  MediatR + HAL         │     │   │ Sidecar (HTTP)          │         │  Service           │
   │  React 19 (Carbon)     │     │   │  ↓ Azure Queue          │         └─────────┬──────────┘
   │  Power BI embed        │     │   │ Service (BackgroundSvc) │                   │ consumed by
   └────────────┬───────────┘     │   │  ↓ Azure Table auditlog │◀──── all services ┘
                │                 └──▶│  (dead-letter queue)    │     emit audit events
                ▼                     └────────────────────────┘
   ┌────────────────────────┐
   │ Azurite (blob/table/   │
   │ queue) — Aspire local; │
   │ real Azure Storage in  │
   │ production             │
   └────────────────────────┘
```

## Repository layout

```
integrate.SaaS.foundations/
├── auth-server/                 # OIDC issuer + gateway services (net10, Aspire 13.1)
│   ├── .aspire/                 # Aspire settings
│   ├── .pipelines/              # 6 Azure DevOps build pipelines
│   ├── docs/                    # readme.md, timeouts.md, AUTHENTICATION_COOKIE_CONFIGURATION.md
│   └── src/                     # Integrate.SaaS.AuthServer.sln + 22 projects
│
├── audit-log/                   # Distributed audit-log (net10, Aspire 13.0.2)
│   └── src/                     # Integrate.SaaS.AuditLog.sln + 11 projects
│       └── .pipelines/          # client, service, sidecar pipelines
│
├── tenant-admin/                # Tenant admin API + React SPA (net9, Aspire 9.4.2)
│   ├── .pipelines/              # tenantadmin.api + tenantadmin.web pipelines
│   ├── docs/decisions/          # ADR001 audit log
│   ├── spikes/                  # Power BI / Fabric spike
│   └── src/
│       ├── Integrate.Gateway.TenantManager.{AppHost,Api,Web,Ingress,ServiceDefaults}/
│       ├── Integrate.Gateway.TenantManager.Api.Tests/
│       └── ClientApp/           # React 19 + Vite + TanStack + Carbon
│
├── reports/                     # Power BI embedded NuGet library (net9)
│   └── src/{Integrate.SaaS.Reports,Integrate.SaaS.Reports.Tests}/
│   └── .pipelines/reports.build.yaml
│
├── .github/{CODEOWNERS,dependabot.yml}
├── .pipelines/templates/{common-variables,build-docker-image}.yaml
├── .vscode/tenant-manager.code-workspace
├── Makefile, docker.mk          # root make dispatch
├── auth.ps1 / auth.sh           # `az login` helpers (Milliman tenant)
├── env-local.template           # DEVOPS_PAT placeholder
└── README.md
```

## Entry points

### auth-server

| Service | Entry | Ref |
|---|---|---|
| Aspire AppHost | `AppHost.cs` | `auth-server/src/Integrate.SaaS.AuthServer.AppHost/AppHost.cs:1-282` |
| IssuerService | `Program.cs` (top-level) | `auth-server/src/Integrate.SaaS.AuthServer.IssuerService/Program.cs:1-101` |
| Api | `Program.cs` | `auth-server/src/Integrate.SaaS.AuthServer.Api/Program.cs:1-105` |
| PortalService | `Program.cs` | `auth-server/src/Integrate.SaaS.AuthServer.PortalService/Program.cs:1-158` |
| Ingress (YARP) | `Program.cs` | `auth-server/src/Integrate.SaaS.AuthServer.Ingress/Program.cs:1-13` |
| ConfigurationService | `Program.cs` | `auth-server/src/Integrate.SaaS.AppServices.ConfigurationService/Program.cs:1-88` |
| UpdateService | `Program.cs` | `auth-server/src/Integrate.SaaS.AppServices.UpdateService/Program.cs:1-105` |
| WebHookRouterService | `Program.cs` | `auth-server/src/Integrate.SaaS.AppServices.WebHookRouterService/Program.cs:1-36` |
| SampleApp (Blazor) | `Program.cs` | `auth-server/src/Integrate.SaaS.AuthServer.SampleApp/Program.cs` |

OIDC surface (IssuerService) — `/authorize`, `/token`, `/.well-known/openid-configuration`, JWKS, `/logout` (wired via `OpenIdIssuerExtensions.cs:1-90`, `WellKnownMiddleware`, `LogoutEndpointRouteBuilderExtensions`).

### tenant-admin

| Project | Entry | Ref |
|---|---|---|
| AppHost | `AppHost.cs:4,68` | `tenant-admin/src/Integrate.Gateway.TenantManager.AppHost/AppHost.cs` |
| Api | `Program.cs:13,76` | `tenant-admin/src/Integrate.Gateway.TenantManager.Api/Program.cs` |
| Web (static SPA host) | `Program.cs:6,15` | `tenant-admin/src/Integrate.Gateway.TenantManager.Web/Program.cs` |
| Ingress | `Program.cs:1-13` | `tenant-admin/src/Integrate.Gateway.Ingress/Program.cs` |

Controllers (under `Integrate.Gateway.TenantManager.Api/Controllers/`):

- `TenantManagerController.cs:18` — `GET Index`, `GET AdminCheck`.
- `TenantController.cs:14` — `GET Details`, `POST UpdateTier` (OpsAdmin).
- `GroupsController.cs:15` — `POST Add`, `POST Delete`.
- `RolesController.cs:16` — `POST Add`, `POST Delete`, `POST UpdatePermissions`.
- `EnvironmentsController.cs:15` — `GET Details`, `POST AddGroupRoles`, `POST DeleteGroupRoles`, `POST UpdateGroupRing`.
- `ExecutionEnvironmentsController.cs:13` — `GET Summary`, `GET List`.
- `ReportsController.cs:17` — `GET ListReports`, `GET {workspaceId}/{reportId}` (FeatureGate `Reporting`).
- `ConfigController.cs:11` — `GET config/milliman.config.js`, `GET api/config/Data`.

### audit-log

| Service | Entry | Ref |
|---|---|---|
| Sidecar | `Program.cs:7,40` | `audit-log/src/Integrate.SaaS.AuditLog.Sidecar/Program.cs` — `POST /api/v1/audit` |
| Sidecar handler | `AuditEventEndpoint.cs:10` | `audit-log/src/Integrate.SaaS.AuditLog.Sidecar/AuditEventEndpoint.cs` |
| Service (worker) | `Program.cs:7,42,55` | `audit-log/src/Integrate.SaaS.AuditLog.Service/Program.cs` |
| BackgroundService | `AuditEventQueueService.cs:5-7` | `audit-log/src/Integrate.SaaS.AuditLog.Service/AuditEventQueueService.cs` |
| Queue processor | `AuditEventQueueProcessor.cs:12` | `audit-log/src/Integrate.SaaS.AuditLog.Service/` |
| AppHost | `AppHost.cs:4,30,37` | `audit-log/src/Integrate.SaaS.AuditLog.AppHost/AppHost.cs` |
| Client SDK | `AuditLogClient.cs:7,10` | `audit-log/src/Integrate.SaaS.AuditLog.Client/AuditLogClient.cs` |
| Client DI | `ServiceCollectionExtensions.cs:7` | `audit-log/src/Integrate.SaaS.AuditLog.Client/ServiceCollectionExtensions.cs` |

### reports

| Surface | Entry | Ref |
|---|---|---|
| DI | `AddEmbeddedReports<TBuilder>` | `reports/src/Integrate.SaaS.Reports/ServiceCollectionExtensions.cs:9` |
| OTel | `AddEmbeddedReportsInstrumentation` | `reports/src/Integrate.SaaS.Reports/ServiceCollectionExtensions.cs:21` |
| Public API | `IEmbeddedReportService` | `reports/src/Integrate.SaaS.Reports/IEmbeddedReportService.cs:5-36` |
| Impl | `EmbeddedReportService` | `reports/src/Integrate.SaaS.Reports/EmbeddedReportService.cs:11` |

## Data flow

### OIDC login (auth-server + consumer)

1. User hits a consumer app (tenant-admin Web, PortalService, SampleApp).
2. Consumer redirects to IssuerService `/authorize` (through `Ingress`).
3. IssuerService federates to upstream IdP (Keycloak dev / Auth0 / Entra) using `OpenIdConnect`.
4. IssuerService builds local `AuthorizationData` (scope, redirect_uri, nonce, state), persists an `AuthorizationCode` in blob (`auth-codes`), returns code.
5. Consumer exchanges code at `/token`; `ProtocolResponseFactory` mints access + id + refresh tokens (refresh persisted in `auth-refresh` blob), issuing custom Milliman claims (`tenant`, `environment`, `ring`, `permission`, `group`, `tier`, `customer`, …).
6. Resource APIs (Api, Config, Update) validate JwtBearer, run the `Client/Middleware` stack (`ClaimsRequestContext`, `TenantMiddleware`, `EnvironmentMiddleware`, `UpdateRingMiddleware`, `AuthorizeMiddleware`) to hydrate per-request context.
7. Cleanup: `AuthorizationCodeCleanupService` + `RefreshTokenCleanupService` prune expired records.

### Tenant admin CQRS + audit

1. HTTP request → ASP.NET controller → MediatR `Send` of a `*Request` (e.g. `GroupAddRequest`).
2. MediatR pipeline runs `AuditBehavior<,>` (`AudiLogExtensions.cs:17`) before/after the handler.
3. Handler reads/writes JSON blobs via `TenantStore`, `AppStore`, `EnvironmentStore` (`auth-tenants` container).
4. `AuditBehavior` appends to `auth-auditlog` append-blob via `RollingAuditBlobLogger` (`AuditLog/RollingAuditBlobLogger.cs:42-78`) with rollover at 49,000 events (ADR001).
5. Response is shaped into HAL via `Api/Hypermedia/*Representation`.

### Audit-log (cross-service)

1. Any service depends on `Integrate.SaaS.AuditLog.Client` NuGet; calls `IAuditLog.LogEventAsync(AuditEvent)`.
2. Client posts JSON to sidecar at `http://{sidecar}:5002/api/v1/audit`.
3. Sidecar (`AuditEventEndpoint.cs:10`) JSON-serializes + Base64-encodes + enqueues on `audit-events` (Azure Queue).
4. Service (`AuditEventQueueService`) polls the queue in batches (`MaxMessagesPerBatch=32`, visibility `1m`, `MaxDequeueCount=5`).
5. For each message: parse → build `AuditEventEntity` (PK=ServiceName, RK=descending ticks) → `AuditLogRepository.StoreAsync` inserts into `auditlog` Table. Duplicates (HTTP 409) → delete (idempotent). Poison / over-retried → `audit-events-dl`.

### Power BI embed (reports → tenant-admin)

1. `ReportsController` checks `CostManagementReader` policy.
2. Resolves caller's `customer` claim, calls `IEmbeddedReportService.ListReportsAsync` (reports NuGet).
3. Library uses MSAL (`Microsoft.Identity.Client`) with a configured `ReportOptions` to get a bearer token, then uses `Microsoft.PowerBI.Api` to list workspaces/reports and mint an embed token.
4. SPA page renders with `powerbi-client-react`.

## Module / package layout

### auth-server (22 projects)

Deployable:
- `Integrate.SaaS.AuthServer.IssuerService` (OIDC issuer — MVC).
- `Integrate.SaaS.AuthServer.Api` (user/group REST).
- `Integrate.SaaS.AuthServer.PortalService` (admin MVC UI).
- `Integrate.SaaS.AuthServer.Ingress` (YARP).
- `Integrate.SaaS.AppServices.ConfigurationService`.
- `Integrate.SaaS.AppServices.UpdateService`.
- `Integrate.SaaS.AppServices.WebHookRouterService`.
- `Integrate.SaaS.AuthServer.SampleApp` (Blazor Server reference).

Libraries:
- `Integrate.SaaS.AuthServer` — core issuer (Abstractions, Data, Services, WellKnownEndpoints, Logout, Model, Mvc, Diagnostics, AuditLog).
- `Integrate.SaaS.AuthServer.Client` — middlewares, `TicketStore`, OIDC events, builder extensions consumed by every service.
- `Integrate.SaaS.AuthServer.ServiceDefaults` — Aspire defaults.

Aspire + tests: `AppHost`, `TestToolkit`, `Tests`, `IssuerService.Tests`, `Api.Tests`, `Client.Tests`, `PortalService.Tests`, `ConfigurationService.Tests`, `UpdateService.Tests`, `WebHookRouterService.Tests`.

### tenant-admin

- `Integrate.Gateway.TenantManager.AppHost` (Aspire).
- `Integrate.Gateway.TenantManager.Api` (main API — MediatR `Api/Commands` + `Api/Handlers`, HAL `Api/Hypermedia`, `Data/` stores, `AuditLog/`).
- `Integrate.Gateway.TenantManager.Web` (static host for the SPA).
- `Integrate.Gateway.Ingress` (YARP, local-dev host-based routing on port 44300).
- `Integrate.Gateway.TenantManager.ServiceDefaults` (imports `Integrate.SaaS.Reports`).
- `Integrate.Gateway.TenantManager.Api.Tests` (xunit.v3 + Testcontainers + Aspire TestHost).
- `ClientApp/` — React SPA (`src/{auth,components,models,pages,routes,store,styles,utils}`, `e2e/`, `test/{integration,mock-api}`).

### audit-log (11 projects)

- `Integrate.SaaS.AuditLog.Api` (contract).
- `Integrate.SaaS.AuditLog.Client` (multi-TFM NuGet SDK).
- `Integrate.SaaS.AuditLog.Sidecar` (HTTP ingress).
- `Integrate.SaaS.AuditLog.Service` (queue → table worker).
- `Integrate.SaaS.AuditLog.ServiceDefaults` (Aspire).
- `Integrate.SaaS.AuditLog.AppHost` (Aspire orchestrator).
- `Integrate.SaaS.TestToolkit` (shared fixtures).
- Test projects: `Client.Tests`, `Service.Tests`, `Sidecar.Tests`, `IntegrationTests` (xunit.v3 + `Aspire.Hosting.Testing`).

### reports (2 projects)

- `Integrate.SaaS.Reports` (NuGet-packable class library; target `net9.0`).
- `Integrate.SaaS.Reports.Tests` (xunit + FakeItEasy).

## Cross-cutting concerns

- **Service discovery** — Aspire `Microsoft.Extensions.ServiceDiscovery` 10.0.0. YARP destinations like `https://issuer`, `https://user-service` are resolved by Aspire (`Ingress/Program.cs:7` `AddServiceDiscoveryDestinationResolver`).
- **Resilience** — every HttpClient gets `AddStandardResilienceHandler` in `ServiceDefaults.Extensions` (`audit-log/src/Integrate.SaaS.AuditLog.ServiceDefaults/Extensions.cs:32-39`; same pattern in auth-server ServiceDefaults).
- **Observability** — OpenTelemetry across all services, exported to OTLP + Azure Monitor; semantic conventions in `audit-log/src/Integrate.SaaS.AuditLog.ServiceDefaults/SemanticConventions.cs`.
- **Security headers** — `OwaspHeaders.Core.UseIntegrateSecurityHeaders()` on every auth-server service.
- **Data Protection / DPAPI** — cert-based, blob-backed key ring (per service: `auth-dpapi`, `portal-dpapi`, `sample-dpapi`). Dev certs generated via `DevCertificateExtensions.cs` + mkcert.
- **Cookie ticket store** — `auth-server/src/Integrate.SaaS.AuthServer.Client/Authentication/TicketStore.cs` persists large session tickets in blob.
- **Feature flags** — `Microsoft.FeatureManagement` with `DisabledFeatureHandler` (tenant-admin `Api/Program.cs:25-26`, `FeatureFlags.cs`).
- **Swagger** — tenant-admin Api at `/api/services/tenant-manager/swagger` (`Program.cs:59-64`).
- **Audit behavior** — MediatR `AuditBehavior<,>` emits events for every command in tenant-admin (see ADR001).

## Ingress routing (auth-server YARP)

From `auth-server/src/Integrate.SaaS.AuthServer.Ingress/appsettings.json:10-128`:

| Route | Path pattern | Cluster → destination (service-discovery URI) |
|---|---|---|
| `issuer` | `/**` (catch-all) | `issuer` → `https://issuer` |
| `config` | `configuration/**` | `config` → `https://configuration-service` |
| `users` | `users/**` | `api` → `https://user-service` |
| `groups` | `groups/**` | `api` → `https://user-service` |
| `portal` | `portal/**` | `portal` → `https://portal-service` |
| `portal-auth` | `signin-portal/**` | `portal` → `https://portal-service` |
| `downloads` | `api/downloads/**` | `updater` → `https://update-service` |
| `release-notes` | `api/releasenotes/**` | `updater` → `https://update-service` |
| `webhook` | `**` (header-gated: `aeg-subscription-name`, `aeg-event-type`) | `webhook` → `https://webhook-router` |
