# Features Index — rainier

## Business features

User- and customer-visible capabilities.

| Feature | Summary |
|---|---|
| [Project Authoring](business/project-authoring.md) | Create, edit, organize Integrate projects with models, ETLs, and metadata. |
| [Project Lifecycle (fork/import/archive)](business/project-lifecycle.md) | Fork, import (IDMX/zip), archive, rename, delete, replace, copy, move projects. |
| [Branches & Commits](business/branches-and-commits.md) | Create, rename, and sync branches; work with commits; checkout, revert. |
| [Sync, AutoMerge, SyncMerge](business/sync-and-merge.md) | Pull cloud changes and reconcile via automatic or user-assisted merge. |
| [Conflict Resolution](business/conflict-resolution.md) | Three-way merge UI (Developer Desktop) for resolving GDF-node-level conflicts. |
| [Release & Promote (IFP/IDM)](business/release-and-promote.md) | Publish commits and promote models to IFP and IDM environments. |
| [Run Projections](business/run-projections.md) | Start, cancel, monitor, and retrieve results of projection jobs (local, grid, cloud). |
| [Job Management](business/job-management.md) | Job history, status, folder picker, batched downloads. |
| [MG-ALFA Classic Editors](business/mg-alfa-classic-editors.md) | Database, Input, Table, Formula DB, Inforce editors (WinForms/WPF + APL). |
| [Formula DB & Validation](business/formula-db.md) | Author/edit MG-ALFA formulas with parsing, validation, and refactoring (rename variable). |
| [Excel Table Import/Export](business/excel-tables.md) | Round-trip .Atb2 tables with Excel workbooks via OpenXML. |
| [ETL Authoring](business/etl-authoring.md) | Author ETL jobs for data ingress into model inputs. |
| [Model Point File Import](business/model-point-file-import.md) | Import seriatim policies into a model via governed endpoint. |
| [Governance (metadata, settings, calc engine upgrade)](business/governance.md) | Governed edits: metadata, projection settings (bulk & per), change database, break references, calc-engine upgrade, project notes. |
| [Workspace Templates](business/workspace-templates.md) | Named projection-configuration templates applied per branch/project. |
| [MMD Collaborative Development](business/mmd-collaborative-development.md) | Developer Desktop + Developer Site for collaborative shared-model authoring with live updates. |
| [Conflict Resolution in MMD](business/mmd-conflict-resolution.md) | GDF-node three-way merge inside MMD Developer Desktop. Cross-links: [Conflict Resolution](business/conflict-resolution.md), [dev Conflict Resolution engine](dev/conflict-resolution-engine.md). |
| [ACL / Permissions](business/acl-permissions.md) | Owner/Read/Write permissions on projects and resources. |
| [Intex Integration](business/intex-integration.md) | Run Intex structured-finance deals as part of projections. |
| [Cloud Run Storage (Origin)](business/cloud-run-storage.md) | SAS-backed upload/download of inputs/outputs for cloud projection jobs. |
| [Integrate Auth & Gateways](business/integrate-auth-and-gateways.md) | Gateway selection + OIDC auth at app startup. |
| [In-app AI Assistant (LAPA Copilot Chat)](business/in-app-ai-assistant.md) | Integrated chat assistant, toggle-gated. |
| [Audit Reports & Change Details](business/audit-and-change-details.md) | View change history and audit reports per project. |
| [Usage Tracking](business/usage-tracking.md) | Per-project usage / what's consumed and where. |
| [Comparison View](business/comparison-view.md) | Compare projects, branches, or commits side-by-side. |
| [ESG Scenarios](business/esg-scenarios.md) | Risk-neutral ESG .bin scenario data shipped quarterly. |
| [User Docs (PDFs)](business/user-docs.md) | UserGuide, ScriptingGuide, ReleaseNotes delivered as PDFs. |
| [Feature Toggles (user-facing)](business/feature-toggles.md) | User-controllable toggles via `feature-toggles.json`. Cross-link: [Feature Toggles (dev)](dev/feature-toggles-infrastructure.md). |
| [Release Notes](business/release-notes.md) | Customer-facing release notes surfaced in-app. Cross-link: [Release Note Generation (dev)](dev/release-note-generation.md). |
| [Swagger API Explorer](business/swagger-api-explorer.md) | Live API docs for Project Manager (also used by devs — see [dev](dev/swagger-api-explorer.md)). |

## Dev features

Build, CI, tooling, internal developer experience.

| Feature | Summary |
|---|---|
| [CI Pipelines (Azure DevOps)](dev/ci-pipelines.md) | Per-service, per-subproject Azure DevOps pipelines with shared templates. |
| [Shared Pipeline Templates](dev/shared-pipeline-templates.md) | Reusable `dotnet-build.yml` and `docker-build.yml` job templates. |
| [Playwright E2E Orchestration](dev/playwright-e2e-orchestration.md) | 14-stage E2E test catalog with app-installer steps for Electron app. |
| [Playwright CLI Skill (AI-assisted)](dev/playwright-cli-skill.md) | `.github/skills/playwright-cli` — AI attaches to running Electron app via CDP. |
| [Versioning Pipelines](dev/versioning-pipelines.md) | Build/release version calculation + PS helpers. |
| [GitHub Release Automation](dev/github-release-automation.md) | Containerized tools for tag-build and release-notes-PR generation. |
| [Main → SDK Branch Sync](dev/main-to-sdk-sync.md) | GitHub Action that mirrors `main` into the latest `release/sdk/v*`. |
| [Cross-repo Makefile Utilities](dev/cross-repo-makefiles.md) | `shared/make/*.mk` — Three Musketeers pattern. |
| [NuGet & npm Feed Setup](dev/package-feed-setup.md) | Azure DevOps feeds + `vsts-npm-auth` + PAT-based NuGet auth. |
| [Feature Toggles (infrastructure)](dev/feature-toggles-infrastructure.md) | `FeatureToggleConfig`, `AppToggles`, JSON-to-config bridge, renderer proxy. Cross-link: [business](business/feature-toggles.md). |
| [Dev Flags](dev/dev-flags.md) | `dev-flags.json` mechanics for mixing bundled + locally-running dev components. |
| [SignalR Hubs](dev/signalr-hubs.md) | Real-time status channels per local service + ACL. |
| [MediatR CQRS Pipeline](dev/mediatr-cqrs.md) | `CommandPipelineBehavior` + `CommandDispatcher` in Rainier.Server.Core. |
| [JWT Auth (Online + Offline)](dev/jwt-auth.md) | `Rainier.ServiceToolkit` JWT; offline-lifetime for desktop-local services. |
| [RequiresPermission Authorization](dev/requires-permission.md) | Attribute-based permission gates + claims middleware. |
| [Startup Upgrader (schema migrations)](dev/startup-upgrader.md) | DB/schema migrations applied at service startup. |
| [Hypermedia (HAL) Responses](dev/hypermedia-hal.md) | HAL+JSON representations via `Milliman.Hal`. |
| [Health Checks & /version endpoints](dev/health-checks-and-version.md) | Standard well-known endpoints across services. |
| [Storybook](dev/storybook.md) | Component catalog + Playwright visual tests; publish pipeline. |
| [Conflict Resolution Engine](dev/conflict-resolution-engine.md) | `ConflictResolution.Core` three-way merge library. Cross-link: [business MMD Conflict Resolution](business/mmd-conflict-resolution.md). |
| [GDF File Format Library](dev/gdf-file-format.md) | `Milliman.MGAlfa.Core/Gdf/*` entity-node serializer + upgrade steps. |
| [DesktopCore Test Tools](dev/desktop-core-test-tools.md) | Shared test artifacts + fixtures. |
| [CodeGenerator](dev/code-generator.md) | Build-time source generation for formats/serializers. |
| [APL Interface / Transactions](dev/apl-interface.md) | C# ↔ Dyalog APL transaction RPC layer. |
| [UDF Code Generation](dev/udf-code-generation.md) | Compile user-defined formulas to native code. |
| [Projection Execution Regression Suite](dev/projection-regression-suite.md) | Baseline-diff regression runner for projection correctness. |
| [Calc Engine Bundles](dev/calc-engine-bundles.md) | Versioned calc engine nuget packaging per release. |
| [SDK AI Agent](dev/sdk-ai-agent.md) | `.github/agents/sdk-agent/` multi-file agent spec for Calc Engine SDK work. |
| [AI Prompts Library](dev/ai-prompts-library.md) | `.github/prompts/` for PR creation, release notes, test plans, flag removal, pipeline triage. |
| [GitHub Instructions (Copilot)](dev/github-instructions.md) | Scoped instructions for frontend and release notes. |
| [Skill Creator Meta-skill](dev/skill-creator.md) | Tooling for authoring new Copilot/OpenCode skills. |
| [ADRs & How-To Docs](dev/adrs-and-how-to.md) | `docs/decisions/`, `docs/how-to/`, `docs/devex/`. |
| [User Docs Build (PDF)](dev/user-docs-build.md) | PS-driven PDF generation pipeline for user-facing docs. |
| [Release Note Generation](dev/release-note-generation.md) | AI-assisted release note drafting workflow. Cross-link: [business release notes](business/release-notes.md). |
| [Swagger API Explorer (Project Manager)](dev/swagger-api-explorer.md) | Interactive API testing at `localhost:50202/swagger`. Cross-link: [business](business/swagger-api-explorer.md). |
| [CODEOWNERS](dev/codeowners.md) | Per-path review ownership. |
| [Trivy + Checkov Image Scanning](dev/image-scanning.md) | Security scanning in shared docker_image.mk. |
| [Azurite Emulator for Tests](dev/azurite-for-tests.md) | Local Azure Storage emulator targets. |
| [Dev CLI Tools](dev/dev-cli-tools.md) | AppInsightsClient, DevOpsClient, IconMaker, ScreenRecorder, support apps. |
| [VS Code Workspace](dev/vscode-workspace.md) | `rainier.code-workspace` multi-root definition. |
| [Code Coverage Threshold](dev/code-coverage-threshold.md) | `azurepipelines-coverage.yml` 70% diff target. |
