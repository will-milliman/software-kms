# integrate.SaaS.foundations — Overview

- **Repo:** `milliman-lts/integrate.SaaS.foundations`
- **URL:** https://github.com/milliman-lts/integrate.SaaS.foundations
- **Default branch:** `main`
- **Seed date:** 2026-04-21
- **Custodian:** `@milliman-lts/platform-engineering` (frontend co-ownership for ClientApp, Makefile, README, `.pipelines`, `.github`).

## Purpose

`integrate.SaaS.foundations` is a **multi-service monorepo** containing the foundational platform services for the **Integrate SaaS** product line (Milliman). It provides identity/authentication, tenant administration, audit logging, software update distribution, tenant configuration, webhook routing, and embedded reporting — all orchestrated locally with **.NET Aspire** and deployed as containers to Azure via Azure DevOps pipelines.

The repo is the back-of-house for Milliman's `MG-ALFA` / "Integrate" family of applications, sitting behind a shared **YARP ingress gateway** and federating upstream to Auth0 / Entra ID.

## Target audience

- **Platform engineering** — custodians and primary developers.
- **Frontend platform engineering** — owners of the Tenant Admin React SPA.
- **Consumers** (other Integrate product teams) — integrate via the OIDC issuer, the REST APIs, the `Integrate.SaaS.Reports` NuGet, and the audit-log client SDK.

## High-level capabilities

Organized by the four top-level service folders:

1. **`auth-server/`** — Self-hosted OIDC/OAuth2 **Issuer** (custom-built, not IdentityServer/Duende) + a family of gateway services:
   - `IssuerService` — `/authorize`, `/token`, `/.well-known/openid-configuration`, JWKS, `/logout`.
   - `Api` — user and group management REST API.
   - `PortalService` — MVC admin portal.
   - `ConfigurationService` — per-tenant / ring / environment configuration API.
   - `UpdateService` — software update artifact downloads + release notes.
   - `WebHookRouterService` — Azure EventGrid webhook ingress.
   - `Ingress` — YARP reverse-proxy gateway.
   - `SampleApp` — Blazor Server reference OIDC client.
2. **`tenant-admin/`** — ASP.NET Core API + React (Vite/TanStack/Carbon) SPA for tenant administration: tenant tier, groups, roles, permissions, rings, environments, execution environments, and embedded Power BI billing/cost-management reports.
3. **`audit-log/`** — Distributed audit logging system: Client SDK (NuGet, multi-TFM) → HTTP Sidecar → Azure Storage Queue → Service (background worker) → Azure Table `auditlog`, with dead-letter queue and idempotent writes.
4. **`reports/`** — `Integrate.SaaS.Reports` NuGet library wrapping Power BI Embedded (bearer-token factory, embed-token generation, report listing); consumed by `tenant-admin` and reusable by other services.

## Build, CI, and local dev summary

- **.NET 10** (auth-server, audit-log) / **.NET 9** (tenant-admin, reports) with **C# 14**.
- **.NET Aspire** AppHosts in each service for local orchestration (Azurite emulator, Keycloak dev IdP, audit-log sidecar, etc.).
- **Docker + docker-compose + Make** ("Three Musketeers" pattern) for reproducible build/test/scan/publish.
- **Azure DevOps Pipelines** (13 build definitions listed in root `README.md:5-16`). Weekly Tuesday rebuilds refresh base images.
- **Shared pipeline templates** at `.pipelines/templates/{common-variables,build-docker-image}.yaml`.
- **Dependabot** — 5 NuGet groups (one per service) + 1 npm group (ClientApp), all with sensible groupings.
- **Private Azure DevOps NuGet feed** (`mgalfadev/Rainier`, `milliman` feed) — requires `DEVOPS_PAT` locally (`env-local.template` / `env-local.mk`).
- **Azure Container Registry** `integratesolutions.azurecr.io` for all images.

See `tech.md`, `architecture.md`, `domain.md`, and `features/_index.md` for depth.
