# rainier — Technology Stack

## Languages & Runtimes

| Language | Where | Notes |
|---|---|---|
| **C# / .NET 8** | `src/server/**`, `src/Rainier.*`, `monorepo/mg-alfa/NUI/**`, `monorepo/model-development/**`, `monorepo/projection-execution/**`, `monorepo/desktop-core/**` | Primary backend + desktop language. Kestrel web hosts, WinForms, WPF. |
| **TypeScript** | `monorepo/desktop/ui/**` | React renderer, Electron main, E2E tests. |
| **C / C++** | `monorepo/desktop-core/**`, native calc engine interop | Legacy GDF formats, calc engine, UDF native support. |
| **Dyalog APL** | `monorepo/mg-alfa/UI/AplSource/**` | Original MG-ALFA model engine — still the ground truth for many calculations. Invoked via `AplInterface` transactions. |
| **PowerShell** | `.pipelines/**`, `tools/scripts/**`, `monorepo/*/scripts/**`, `user-docs/*.ps1` | Build/CI helpers, release automation, PDF doc generation. |
| **Python** | `.github/skills/skill-creator/scripts/*.py`, infra glue | Mostly skill tooling + some build scripts. |
| **HTML / CSS / Less** | Razor views in ASP.NET services; Carbon-based React UI. |
| **Standard ML / Smarty / APL / Common Lisp** | Legacy bits inside `monorepo/mg-alfa/UI/AplSource/` vendor code. |

## Frontend

- **Node:** v18.x (`nvm use 18`).
- **Package manager:** **pnpm 9.15.4** with `pnpm-workspace.yaml` at `monorepo/desktop/ui/pnpm-workspace.yaml:1`.
- **Workspace path aliases:** as of PR #9607, `tsconfig.root.json` no longer maps `@repo/store/*` and `@repo/store/config/*`. Internal `packages/store` files use relative imports; external consumers (`apps/web`, `packages/project-manager`) import via `@repo/store/src/*` and rely on the existing `workspace:*` pnpm dependency for resolution.
- **Framework:** React 19, Vite 6, Electron 34, Redux Toolkit 2.5, RTK Query.
- **Design system:** Carbon Design System wrapped by `@repo/components`.
- **Testing:** Playwright (E2E at `monorepo/desktop/ui/e2e/electron/`), Jest.
- **Storybook:** at `monorepo/desktop/ui/tools/storybook/`; published to `https://ltssharedassets.z13.web.core.windows.net/`.
- **Feature toggles:** `apps/electron/src/options/feature-toggles/app-toggles.ts:26` (~40 `ToggleItem`s), exposed to renderer via `@repo/feature-toggles`.

## Backend (.NET)

- **Runtime:** .NET 8, ASP.NET Core (all hosts use `Rainier.ServiceToolkit.Host.ServiceHost<Program>`).
- **DI:** Autofac (ProjectManager/RunManager/Governance), Castle Windsor (Acl).
- **CQRS:** MediatR 12.5 with `CommandPipelineBehavior` at `src/server/Rainier.Server.Core/Application/CommandPipelineBehavior.cs:29`.
- **Event sourcing:** `ES.Core` in the ACL service.
- **Real-time:** SignalR (+ AzureSignalR in prod for Acl).
- **Auth:** JWT via `Rainier.ServiceToolkit`. Desktop-local services use "offline lifetime" JWT (token issued once per session).
- **Authorization:** `RequiresPermission` attribute at `src/server/Rainier.Server.Core/Security/RequiresPermission.cs`; policies include `ConflictResolution`, `PromoteModelToIfp`, `ManageModelOwner`, `Authoring.Edit`.
- **Error/Problem details:** Hellang.Middleware.ProblemDetails + `HttpClientExceptionHandlingMiddleware` + HAL-style hypermedia via `Milliman.Hal`.
- **Resilience:** Polly circuit breakers on HTTP clients (e.g. ProjectManager → MMD cloud and ACL).
- **Persistence:** No EF DbContexts found. Storage is:
  - Event-sourced (`ES.Core`) in ACL.
  - Azure Blob / Table / Queue in Origin and Application.Host.
  - Local filesystem / working folder for ProjectManager and Governance.
  - SQLite via `StartupUpgrader.UpgradeDbLite` in RunManager.
- **OpenAPI:** Swashbuckle (Origin, RunManager), NSwag (Acl, ProjectManager, Governance).
- **TLS:** `UseSecureKestrel` with embedded `ssl.localhost.pfx` for localhost desktop services.

## MG-ALFA Classic (desktop)

- **.NET WinForms + WPF** main shell at `monorepo/mg-alfa/NUI/NUX/Source/MgAlfa.Executable/App.xaml.cs:63`.
- **Dyalog APL** workspace at `monorepo/mg-alfa/UI/AplSource/` — invoked via transaction classes in `monorepo/mg-alfa/NUI/NUI/Milliman.MGAlfa.AplInterface/`.
- **Build system:** NuGet + **psake** at `monorepo/mg-alfa/BuildSupport/`. Entry scripts: `build.bat`, `build_ui.bat`, `psakefile.ps1`.
- **VSTO-style Excel integration** for Excel tables (`monorepo/mg-alfa/NUI/Managed/Milliman.MGAlfa.Tables.Excel/`, using OpenXML).
- **UDF debug helpers** (VS 2017/2019/2022) at `monorepo/mg-alfa/UdfDebug/`.

## Model Development (MMD)

- **Docker-based** build and test (`monorepo/model-development/Makefile` + `docker-compose.yml` + Three Musketeers pattern).
- **Server:** ASP.NET Web (`Server/WebSite/`), WorkerRole (`Server/WorkerRole/`), Elasticsearch shared (`ServerShared.ES/`).
- **Functions:** Azure Functions (`Functions/`, `docker/Dockerfile.functions-diff`).
- **Desktop:** WPF (`DiffMergeClient/DeveloperDesktop/`), uses SignalR for live collab, launches MG-ALFA editors from `EditLaunchers/`.
- **Conflict resolution:** Three-way merge engine in `src/DiffMergeClient/ConflictResolution/ConflictResolution.Core/`, registered via `ConflictResolution.Autofac/`.
- **Monitoring:** `monitoring/` with backup-schedule tooling (`BackupSchedule.csv`).

## Projection Execution

- **.NET 8** console host `Milliman.LTS.MgAlfa.ProjectionExecution.ConsoleHost.exe` at `monorepo/projection-execution/src/MgAlfa.ProjectionExecution/MgAlfa.ProjectionExecution.ConsoleHost/`.
- **Native calc engine (AHost)** orchestrated via `MgAlfa.Runs.Execution/` and `grid-utils-integration/*.targets` which wire the external GridUtils library (`AhostSolver.exe`, `CellCycle.Merge.exe`, `NestedProjection.JobManager.exe`).
- **Intex** integration in `MgAlfa.Intex/`.
- **Cross-process IPC:** `Milliman.LTS.Communication.Core/` (named pipes / messaging).
- **Regression suite:** `MgAlfa.ProjectionExecution.RegressionTest/` — huge baseline-diff regression runner.

## Major dependencies

| Dependency | Provides |
|---|---|
| `Rainier.ServiceToolkit 9.1.6` | Host bootstrap, JWT auth, health checks, well-known endpoints, claims middleware (private Milliman NuGet). |
| `ES.Core` | Event-sourcing infrastructure used by Acl. |
| `Milliman.Hal 8.0.70` | HAL hypermedia responses. |
| `MediatR 12.5` | CQRS dispatch. |
| `Autofac.Extensions.DependencyInjection` | DI container for most services. |
| `Polly` (`Microsoft.Extensions.Http.Polly`) | Resilience policies on HTTP clients. |
| `Hellang.Middleware.ProblemDetails` | RFC 7807 problem+json. |
| `Swashbuckle` / `NSwag` | OpenAPI doc + client gen. |
| `Asp.Versioning` | API versioning in RunManager. |
| `MessagePack` (v3) | Binary serialization in `Milliman.MGAlfa.Core` (Adb2/Ain2/Atb2 internal node types, key structures). Upgraded from v2 to v3 in PR #9574 with Roslyn-based source-generation suppressed for parity; perf tuning tracked separately. |
| `Milliman.Desktop.CalcEngine` | Calc engine binaries (Nuget bundles in `src/server/Rainier.RunManager/CalcEngine-bundles/`). |
| `@carbon/react` | Design system base. |
| `electron-builder` | NSIS installer, code-signing via `code-signing.cjs`. |
| `vsts-npm-auth` | Auth to Azure DevOps npm feed. |
| `wkhtmltopdf` (containerized) | MMD PDF generation. |
| Dyalog APL (Dyalog101) | APL runtime; installer in `monorepo/mg-alfa/DevTools/Dyalog/`. |

## Infrastructure

- **Package feeds (Azure DevOps):** `central`, `milliman`, `rainier-latest` (NuGet v2); `https://pkgs.dev.azure.com/mgalfadev/_packaging/rainier-latest/npm/registry/` (npm).
- **Auth:** `vsts-npm-auth` for npm, PATs for NuGet.
- **Cloud storage:** Azure Blob / Table / Queue (Origin, Application.Host).
- **Real-time infra:** AzureSignalR in prod (Acl).
- **Container registry:** `integratesolutions.azurecr.io`.
- **Image scanning:** Trivy `0.58.2` (`shared/make/docker_image.mk:26`).
- **Azurite** emulator for local Azure Storage tests (`shared/make/azurite.mk`).
- **Deploy targets:** IFP (Integrate Forecasting Platform), IDM (Integrated Delivery Model) — promote flows in ProjectManager `CommitPublishController.cs`.

## Build tooling & CI

- **Azure DevOps Pipelines (`vsts/azure-pipelines.yml`, `.pipelines/**`, `monorepo/*/.pipelines/**`, `src/*/vsts/**`)** — dozens of per-service pipelines. Shared job templates at `.pipelines/templates/jobs/{dotnet-build,docker-build}.yml`. See `kms/rainier/features/dev/ci-pipelines.md`.
- **GitHub Actions:** one workflow, `.github/workflows/sync-main-to-sdk.yml` — cron + manual sync of `main` into the latest `release/sdk/v*` branch.
- **Coverage:** `azurepipelines-coverage.yml` — 70% diff target.
- **Cross-repo make:** `shared/make/*.mk` (Three Musketeers: make + docker compose + dotnet).
- **E2E orchestration:** extensive Playwright stage catalog at `monorepo/desktop/ui/e2e/.pipelines/` (14 stages + app-installer steps).
- **Doc builds:** `user-docs/.pipelines/docs-build.yml` invokes `user-docs/GeneratePdf.ps1` (wkhtmltopdf).
- **Versioning:** `.pipelines/versioning/*.yaml` + PS helpers.
