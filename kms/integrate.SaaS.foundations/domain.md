# Domain

## Glossary

| Term | Meaning |
|---|---|
| **Tenant** | A customer organisation. Identified by `tenantDomain` (e.g. `acme.milliman.com`). Root aggregate for groups, roles, permissions, environments. |
| **Environment** | A named deployment slot inside a tenant (e.g. `dev`, `qa`, `prod`). Owns its own permission/role/ring maps. |
| **ExecutionEnvironment** | A tenant's runtime compute environment — identified by GUID; claim-driven per-environment permissions. |
| **Ring** | A rollout ring for software updates and feature flags (default `"stable"`). Maps groups → rings. Configuration and updates are partitioned by ring. |
| **Group** | An AAD/upstream-IdP group mapped into a tenant. `GroupId → DisplayName`. Groups carry roles, which carry permissions. |
| **Role** | A named bundle of permissions inside a tenant or environment. Editable per-environment. |
| **Permission** | A capability key such as `MGA.Project.Import`. Global catalog in `app.json` (`AppModel.Permissions`); custom permissions per environment. |
| **Tier** | A commercial tier (int) at tenant level, updated by OpsAdmin. Filters which permissions are visible. |
| **OpsAdmin** | A globally-privileged principal (listed in `app.json`). May change any tenant's tier, see ops-only permissions. |
| **TenantAdmin** | A principal listed in `TenantModel.TenantAdmin` — may administer a specific tenant. |
| **Customer ID** | A claim issued by the Integrate Gateway used to resolve Power BI embedded reports for billing/cost-management. |
| **Claim** | A custom Milliman JWT claim at `http://schemas.milliman.com/identity/claims/*` — `tenant`, `environment`, `ring`, `permission`, `group`, `tier`, `location`, `customer`. |
| **Issuer** | The auth-server OIDC endpoint that mints tokens carrying the claim set above. Federates upstream to Keycloak (dev) / Auth0 / Entra ID (prod). |
| **Ingress** | The public YARP reverse proxy that fronts all auth-server services, plus a local-dev simulator in tenant-admin. |
| **Ring-based rollout** | Deployment/config strategy where an environment routes traffic for a group to a specific ring (`stable`, `beta`, etc.). |
| **AuditEvent** | A canonical record of a platform action (`ServiceName`, `EventType`, `Actor`, `Request`, `Outcome`, `OccurredAt`, `Details`, `GroupClaims`, `PermissionClaims`). See `audit-log/src/Integrate.SaaS.AuditLog.Api/AuditEvent.cs:3-16`. |
| **Sidecar** | Per-host HTTP receiver that decouples callers from the audit-log storage backend. |
| **Dead-letter queue** | `audit-events-dl` — receives poison/over-retried audit messages (`MaxDequeueCount = 5`). |
| **DPAPI** | ASP.NET Data Protection keyring, stored in Azure Blob and encrypted by an X.509 cert (dev cert generated via mkcert). |
| **Ticket store** | Blob-backed `ITicketStore` that persists large auth cookie payloads outside of the cookie itself. |
| **HAL** | Hypertext Application Language — HAL+JSON documents used for hypermedia navigation; produced by `Milliman.Hal` in tenant-admin and UpdateService. |
| **MG-ALFA** | Target audience / product brand. Default JWT audience is `mg-alfa.milliman.com` (tenant-admin `Constants.cs:54`). |
| **Gateway** | Marketing/namespace term; the family `Integrate.Gateway.*` sits behind the shared Integrate Gateway. |

## Core entities

### Auth-server domain (`auth-server/src/Integrate.SaaS.AuthServer/Model/`)

- `Tenant`, `TenantEnvironment`, `TenantEnvironmentKey`, `TenantEnvironmentSelection`, `UniqueIdentifier` — tenant identity & selection.
- `ClientApplication`, `ClientCredentials`, `ClientAuthorizeRequest` — OIDC clients.
- `User`, `UserInfo`, `GroupModel` — user/group records.
- Protocol types:
  - `AuthorizeRequest` / `AuthorizeResponse`, `AuthorizationData`
  - `TokenIssueRequest` / `TokenIssueResponse`, `RefreshedTokenResponse`, `RefreshTokenData`
  - `LogoutData`, `OpenIdDiscoveryDocument`, `Prompt`, `TokenTime`, `ValidationResponse`.
- Cleanup: `AuthorizationCodeCleanupService`, `RefreshTokenCleanupService` background workers.

Stores (`auth-server/src/Integrate.SaaS.AuthServer/Data/`): `TenantStore`, `ClientStore`, `UserStore`, `IndexedUserStore`, `AuthorizationCodeStore`, `RefreshTokenStore` — all Azure Blob.

### Tenant-admin domain (`tenant-admin/src/Integrate.Gateway.TenantManager.Api/Models/`)

- **`TenantModel`** (`TenantModel.cs:5-20`) — `Domain`, `Name`, `GroupClaimType`, `NameClaimType`, `ChallengeParameters`, `Groups`, `Roles`, `PermissionMap` (role→permissions), `TenantAdmin`, `Tier`.
- **`EnvironmentModel`** (`EnvironmentModel.cs:5-21`) — `FriendlyName`, `TenantDomain`, `Name`, `Authoring`, `CustomPermissions`, `PermissionMap`, `RoleMap`, `RingMap`.
- **`ExecutionEnvironmentModel`** — `Id`, `Name`.
- **`AppModel`** — global `Permissions`, `Rings`, `OpsAdmin`.
- **`PermissionModel`** — `Id`, `Key` (e.g. `MGA.Project.Import`), `Section`, `Name`, `Description`, `Tier`, `OpsOnly`. Plus `CustomPermissionModel : PermissionModel` and `PermissionDescription`.
- **`FrontEndConfigurationModel`** — emitted as `config/milliman.config.js` for SPA bootstrap.

MediatR commands: `AppQueryRequest`, `TenantQueryRequest`, `TenantTierUpdateRequest`, `GroupAddRequest`, `GroupDeleteRequest`, `RoleAddRequest`, `RoleDeleteRequest`, `RolePermissionUpdateRequest`, `GroupRoleAddRequest`, `GroupRoleDeleteRequest`, `RingUpdateRequest`, `EnvironmentQueryRequest`, `ExecutionEnvironmentQueryRequest`.

HAL documents & representations: `AdminDocument`, `AppDocument`, `EnvironmentDocument`, `ExecutionEnvironmentDetailsDocument`, `ExecutionEnvironmentSummaryDocument`, `TenantDocument`.

### Audit-log domain (`audit-log/src/Integrate.SaaS.AuditLog.Api/AuditEvent.cs:3-34`)

- **`AuditEvent`** (record, server contract): `Version="1.0"`, `ServiceName`, `EventId:Guid`, `EventType`, `OccurredAt`, `Actor`, `Request`, `Outcome`, `Details`, `GroupClaims`, `PermissionClaims`.
- **`ActorInfo`** — `UserId?`, `Subject?`.
- **`RequestInfo`** — `IpAddress`, `UserAgent`, `CorrelationId`.
- **`OutcomeInfo`** — `Status`, `Reason`.
- **Storage** — `AuditEventEntity` (`Service/AuditEventEntity.cs:6-28`): `PartitionKey = ServiceName`, `RowKey = descending-ordered ticks` (`EntityBuilder.cs:30-33`). Collections stored as JSON strings (`Details`, `GroupClaims`, `PermissionClaims`).
- **Client-side copy** (`Client/AuditEvent.cs`) — mutable classes (not records, no `required`) for net6/9 consumer compatibility.

### Reports domain (`reports/src/Integrate.SaaS.Reports/`)

- **`IEmbeddedReportService`** (`IEmbeddedReportService.cs:5-36`) — `ListReportsAsync`, `GetEmbeddedReportAsync`.
- **`ReportOptions`** — configuration (tenant ID, client credentials, workspace scope).
- **`BearerTokenFactory`**, **`PowerBIClientFactory`**, **`ReportClientFactory`** — singleton factories; `EmbeddedReportService` transient.

## Relationships

```
Tenant ─1──n── Environment ─1──n── Role ─1──n── Permission
   │                │                              ▲
   │                └─ RingMap (Group → Ring)      │
   │                └─ PermissionMap (Role → Perms)┘
   │                └─ RoleMap (Group → Role)
   │
   └─ Groups (GroupId → Name)
   └─ TenantAdmin (principals)
   └─ Tier  ──── PermissionModel.Tier gates visibility
   └─ ExecutionEnvironment[] (per-tenant compute envs)

AppModel  ── global Permissions catalog, Rings list, OpsAdmin principals

Issuer ── Tenant ── Environment ── Ring  →  claims on every token:
  tenant, environment, ring, permission, group, tier, location, customer
```

## Business rules encoded in code

### Authorization policies (tenant-admin)

Registered in `tenant-admin/src/Integrate.Gateway.TenantManager.Api/Extensions/ServiceCollectionExtensions.cs:56-66` (implementations in `Extensions/AuthorizationPolicyBuilderExtensions.cs`):

- `RequireOnlyAuthorizedUser` — default baseline.
- `TenantAdmin` — caller must be listed in `TenantModel.TenantAdmin` for the requested tenant.
- `OpsAdmin` — caller is a global ops admin (from `AppModel.OpsAdmin`).
- `CostManagementReader` — caller has `CostManagement.Reports.View` permission.

### Permission visibility (tenant-admin)

- `PermissionModel.Tier` filters the catalog returned by `TenantManagerController.Index` to the subset whose tier ≤ `TenantModel.Tier`.
- `PermissionModel.OpsOnly == true` items are only surfaced to Ops admins.

### Ring defaults (auth-server)

- `DefaultUpdateRing = "stable"` (`auth-server/src/Integrate.SaaS.AuthServer/Constants.cs:21`).
- `Client/Middleware/UpdateRingMiddleware.cs` enforces that a request carries a resolvable ring; downstream Config/Update services partition by `UpdateRingKey`.

### Audit log rollover (tenant-admin)

- Append blob rolls over at **49,000 events** (`tenant-admin/src/Integrate.Gateway.TenantManager.Api/Constants.cs:60`); implemented in `AuditLog/RollingAuditBlobLogger.cs:42-78`. ADR001 (`tenant-admin/docs/decisions/ADR001 - audit log.md`) records the rationale.

### Audit-log poison handling (audit-log)

- `MaxDequeueCount = 5` and `MessageVisibilityTimeout = 1m` in `Service/ServiceOptions.cs`. Messages exceeding retries move to `audit-events-dl` (`QueueMessageHandler.cs:30-34`).
- **Idempotency:** Azure Table duplicate (HTTP 409) → delete message (don't retry).
- **Message format:** Base64(JSON(AuditEvent)) — see `QueueMessageFactory.cs:14-18`.

### Feature gating (tenant-admin)

- The `Reporting` feature flag (`Microsoft.FeatureManagement`) gates the `ReportsController` (`ReportsController.cs:16`). A disabled feature returns a 404 via `DisabledFeatureHandler` (`Api/Program.cs:25-26`).

### Tenant fix-up on startup (tenant-admin)

- `Services/TenantFixupService.cs:10` — a hosted service runs `ITenantFixup.FixAll` at startup to reconcile stale tenant JSON blobs against the current schema.

### EventGrid subscription validation (auth-server)

- The webhook router recognises EventGrid subscription-validation requests (via `aeg-event-type` header on the webhook route) and handles them before dispatch (`WebHookRouterService/WebApplicationBuilderExtensions.cs:13-52`).

### Data Protection key storage

- Every auth-server service persists its DPAPI keyring to its own blob container: `auth-dpapi`, `portal-dpapi`, `sample-dpapi`. Keys are encrypted at rest with an X.509 cert (prod) / mkcert-generated cert (dev).

### OIDC token lifetimes

- `auth-server/docs/timeouts.md` describes the four-layer session model (Entra ID → Auth0 → Issuer → Client cookies). The AppHost currently ships in "SCENARIO 3: TIMEOUT TESTING" with aggressive lifetimes (`auth-server/src/Integrate.SaaS.AuthServer.AppHost/AppHost.cs:55-64`).

### Customer-scoped reports (reports + tenant-admin)

- `IEmbeddedReportService` enumerates Power BI workspaces and filters by the caller's `customer` claim (issued by the Integrate Gateway). Tenant-admin then mints an embed token per report.
