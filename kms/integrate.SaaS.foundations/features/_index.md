# Features — Catalog

Features for `integrate.SaaS.foundations`. "Business" features are user/customer-visible capabilities. "Dev" features are internal developer experience, build, CI, and tooling. A few straddle both categories (marked with ⇆) and have one file per side that cross-link.

## Business

| Feature | Summary |
|---|---|
| [OIDC Issuer](./business/oidc-issuer.md) | Self-hosted custom OIDC/OAuth2 authorization server (authorize, token, discovery, JWKS, logout). |
| [Upstream IdP Federation](./business/upstream-idp-federation.md) | Federates to Auth0 / Entra ID (prod) and Keycloak (dev) for primary authentication. |
| [User & Group Management API](./business/user-group-management.md) | REST API for managing platform users and groups (auth-server `Api`). |
| [Admin Portal](./business/admin-portal.md) | MVC admin portal UI exposed under `/portal/*`. |
| [Tenant Configuration Service](./business/tenant-configuration-service.md) | Per-tenant / ring / environment configuration API. |
| [Software Update Distribution](./business/software-update-distribution.md) | Release notes + range/multipart download API for client software updates. |
| [EventGrid Webhook Ingress](./business/eventgrid-webhook-ingress.md) | Dual-auth EventGrid subscription endpoint with handshake + event dispatch. |
| [Tenant Administration](./business/tenant-administration.md) | API + React SPA for administering tenants (details, admin checks). |
| [Tenant Tier Management](./business/tenant-tier-management.md) | OpsAdmin-only tenant commercial-tier updates that gate permission visibility. |
| [Groups, Roles & Permissions](./business/groups-roles-permissions.md) | Manage tenant groups, roles, and role→permission mappings. |
| [Environment & Ring Rollout](./business/environment-ring-rollout.md) | Per-environment group→role + group→ring mapping for progressive rollout. |
| [Execution Environments](./business/execution-environments.md) | List and inspect per-tenant execution (compute) environments and permissions. |
| [Embedded Power BI Reports](./business/embedded-powerbi-reports.md) | Cost-management / billing reports embedded via Power BI + `CostManagement.Reports.View`. |
| [Ring-Based Rollout](./business/ring-based-rollout.md) ⇆ | Customer-visible ring assignment (`stable`, beta, …) drives which software build / config they see. Also see [dev/ring-based-rollout](./dev/ring-based-rollout.md). |
| [Feature Flags](./business/feature-flags.md) ⇆ | End-user-visible capabilities gated by `Microsoft.FeatureManagement` (e.g. `Reporting`). Also see [dev/feature-flags](./dev/feature-flags.md). |
| [Audit Logging](./business/audit-logging.md) ⇆ | Append-only record of platform actions for compliance. Also see [dev/audit-logging](./dev/audit-logging.md). |

## Dev

| Feature | Summary |
|---|---|
| [Aspire AppHost Orchestration](./dev/aspire-apphost-orchestration.md) | Per-service `.NET Aspire` AppHosts wire up dev dependencies, certs, seed data. |
| [Azurite Local Storage](./dev/azurite-local-storage.md) | Local Azure Storage emulator with persistent volumes and auto-seeded containers/tables/queues. |
| [Keycloak Dev IdP](./dev/keycloak-dev-idp.md) | Keycloak 26.3 container with imported `Integrate` realm for local OIDC federation. |
| [mkcert Dev Certificates](./dev/mkcert-dev-certificates.md) | Locally-trusted token-signing and DPAPI certs generated into `.token-certs/` and `.dpapi-certs/`. |
| [YARP Ingress Gateway](./dev/yarp-ingress-gateway.md) | Path-routed YARP reverse proxy over Aspire service discovery (prod gateway + local simulator). |
| [Three Musketeers Make](./dev/three-musketeers-make.md) | Root `Makefile` + `docker.mk` + per-service `make.mk` for reproducible docker-driven builds. |
| [Docker Compose Test Harness](./dev/docker-compose-test-harness.md) | `src/docker-compose.yaml` services for test, lint, build, Trivy, Checkov. |
| [Azure DevOps Pipelines](./dev/azure-devops-pipelines.md) | 13 build pipelines + weekly Tuesday rebuilds for image refresh. |
| [Shared Pipeline Templates](./dev/shared-pipeline-templates.md) | `.pipelines/templates/{common-variables,build-docker-image}.yaml`. |
| [Dependabot Grouped Updates](./dev/dependabot-grouped-updates.md) | 5 NuGet ecosystems + 1 npm ecosystem with per-service / purpose-based groups. |
| [Checkov Dockerfile Scanning](./dev/checkov-dockerfile-scanning.md) | `make ${prefix}__lint` runs Checkov against each Dockerfile. |
| [Trivy Image Scanning](./dev/trivy-image-scanning.md) | `make ${prefix}__scan_image` runs Trivy against every built image. |
| [OpenTelemetry Observability](./dev/opentelemetry-observability.md) | OTel across all services exported to OTLP + Azure Monitor with custom semantics. |
| [Aspire Hosting Testing](./dev/aspire-hosting-testing.md) | `Aspire.Hosting.Testing` end-to-end integration tests spinning up full distributed app. |
| [React Client Testing Stack](./dev/react-client-testing-stack.md) | Vitest 3 + MSW + Playwright + Testcontainers + Hono mock API. |
| [Audit-Log Client SDK](./dev/audit-log-client-sdk.md) | Multi-TFM NuGet SDK for emitting `AuditEvent`s via an HTTP sidecar. |
| [Power BI Embedded NuGet Library](./dev/powerbi-embedded-nuget.md) | `Integrate.SaaS.Reports` reusable NuGet: bearer factory, report client, embed tokens. |
| [Blazor Sample Client](./dev/blazor-sample-client.md) | Reference Blazor Server OIDC client (`Integrate.SaaS.AuthServer.SampleApp`). |
| [Hono Mock API](./dev/hono-mock-api.md) | Dockerised TypeScript mock API used by integration + E2E tests in the ClientApp. |
| [Data Protection & Ticket Store](./dev/data-protection-and-ticket-store.md) | Cert-based DPAPI + blob-backed cookie ticket store configuration. |
| [Ring-Based Rollout](./dev/ring-based-rollout.md) ⇆ | How ring claims propagate through middleware and partition configuration/updates. Also see [business/ring-based-rollout](./business/ring-based-rollout.md). |
| [Feature Flags](./dev/feature-flags.md) ⇆ | `Microsoft.FeatureManagement` wiring, `DisabledFeatureHandler`, `FeatureGate` attributes. Also see [business/feature-flags](./business/feature-flags.md). |
| [Audit Logging](./dev/audit-logging.md) ⇆ | MediatR `AuditBehavior` + rolling append blob (tenant-admin) + client→sidecar→queue→table (audit-log). Also see [business/audit-logging](./business/audit-logging.md). |
