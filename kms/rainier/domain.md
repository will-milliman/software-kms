# rainier — Domain

## Glossary

| Term | Meaning |
|---|---|
| **MG-ALFA** | "Modeling Generator for Actuaries — Life, Finance, Annuities." Milliman's proprietary actuarial projection system. Dates to the Dyalog APL era and is still the calculation engine of record. |
| **Integrate** | The customer-facing desktop application produced by this repo. Unifies authoring, sync, run, governance, release. |
| **Classic** | The legacy MG-ALFA desktop editor (WinForms/WPF + APL) bundled into Integrate as `DeveloperDesktop.exe` / `MgAlfa.Executable`. |
| **MMD** (Milliman Model Development) | Collaborative authoring product. Split into **Developer Desktop** (WPF client) and **Developer Site** (ASP.NET server + workers + Azure Functions). |
| **Ain2** | Input/Model file format (GDF-encoded). Represents a model configuration. |
| **Adb2** | Database / Formula database file format (GDF-encoded). |
| **Atb2** | Table file format (GDF-encoded). Two-dimensional lookup tables with "IJ" (index-joined) large-table support. |
| **Cmd2** | Command file format (GDF-encoded). |
| **Inforce2** | Inforce (policies) file format (GDF-encoded). |
| **GDF** | "Generic Data Format." Milliman's proprietary binary format used for all MG-ALFA model files. Has entity nodes + versioned `UpgradeSteps` for schema evolution. The node-level structure is what enables MMD's three-way merge. See `monorepo/desktop-core/src/Milliman.MGAlfa.Core/Gdf/`. |
| **Projection** | A single run of a model over time — cell-by-cell actuarial computation. A "projection" in this codebase almost always refers to an MG-ALFA projection. |
| **ETL** | Extract/Transform/Load jobs users author in Integrate to move data between sources and model inputs. UI under `packages/project-manager/src/components/project-manager/authoring.view/etl/`. |
| **IDM** | "Integrated Delivery Model" — Milliman's deployment target platform for released models. |
| **IFP** | "Integrate Forecasting Platform" — another deployment target. Promote flow in `CommitPublishController.cs`. |
| **IDMX** | Export format used when importing/exporting projects from/to IDM. Modals: `import-idmx-modal`, `export-idmx-modal`. |
| **APL** | Dyalog APL — the primary language the original MG-ALFA engine is written in (`monorepo/mg-alfa/UI/AplSource/`). |
| **AplInterface / Txn** | Transaction-based RPC layer bridging C# and APL. Every APL-reachable operation is a `TxnXxx.cs` class. |
| **AHost** | The native (C++) calc engine solver binary. External; wired via `grid-utils-integration/*.targets`. |
| **GridUtils** | External library providing grid-execution primitives (`NestedProjection.JobManager.exe`, `CellCycle.Merge.exe`, `AhostSolver.exe`). Run modes: authoring / local / grid / cloud. |
| **Intex** | Third-party structured-finance deal engine integrated into projections (`MgAlfa.Intex`). |
| **UDF** | User-Defined Formula. Authored by users, compiled to native code via `MgAlfa.RunPreparation.UdfCodeGeneration`. |
| **Run** | A job submitted to execute projections. Managed by **RunManager**. |
| **Workspace** | A per-project working area on disk. `IIntegrateWorkspace`, `WorkingFolderBase` in `Rainier.Core`. Also a Governance concept (`WorkspacesController`) — a template-driven projection configuration. |
| **Project** | The top-level user-facing entity in Integrate. A collection of model files, ETLs, and metadata under shared version control with branches, commits, and ACL-governed access. |
| **Commit / Branch** | Version-control primitives on a project. Published commits go to IFP/IDM; release branches are handled in `authoring.view/branches/`. |
| **Fork** | Create a new project from an existing project's commit. Modal: `modal-tool/modals/fork-*`. |
| **Release Branch** | A branch reserved for release candidates. Created from the "Create Branch" flow. |
| **Sync / AutoMerge / SyncMerge** | Three flavors of reconciling local and remote: **Sync** pulls latest, **AutoMerge** auto-resolves, **SyncMerge** opens the conflict-resolution UI. Endpoints: `ProjectsController.cs:150, :166, :184, :206`. |
| **Promote** | Publish a commit to IFP/IDM. Gated by `PromoteModelToIfp` / `ViewPromote` policies. |
| **ACL Permission** | `Owner`, `Read`, `Write` — resource-based permissions issued by `Rainier.Acl`. |
| **Principal** | Identity (user or group) referenced in an ACL. Types: `AclPrincipalType`. |
| **No-Owner Project** | A project whose owner has been removed/deactivated. Cached separately by ACL. |
| **Gateway** | The cloud endpoint a user selects at startup (Dev, QA, Prod, customer-specific). Managed by `renderers/gateway/` in Electron. |
| **Feature Toggle** | Booleans that gate features. Backend: `FeatureToggleConfig.cs`. Frontend: `apps/electron/src/options/feature-toggles/app-toggles.ts`. User file: `%appdata%/INTEGRATE/feature-toggles.json`. Has three sections: defaults false, defaults true, defaults "internal" (which is gated by the master `Internal` flag). |
| **Internal Toggle** | A toggle whose value is `"internal"` — resolves to true only for internal users (those with the `Internal` master flag set). |
| **Dev Flag** | Separate from feature toggles. `%appdata%/INTEGRATE/dev-flags.json` decides per-component whether the Integrate app uses bundled code (`false`), a bundled dev build (`true`), or an external dev server on a specific port (number). File: `apps/electron/src/options/devFlags.ts:1`. |
| **Workspace template** | A named, governed bundle of projection configuration that can be applied to projects. Managed by `Rainier.Governance/.../WorkspacesController.cs`. |
| **Model Point File** | A file of seriatim policies/contracts used as model input. Imported via Governance `ImportModelPointFile`. |
| **Scenario file** | Economic / demographic scenarios for projection inputs. Files under authoring `scenario-files/`; at rest in GDF. |
| **Factor file** | Assumption factors (mortality, lapse, etc.) used in projections. |
| **Conflict Resolution** | Three-way merge of GDF-node-level changes. Core engine in `monorepo/model-development/src/DiffMergeClient/ConflictResolution/ConflictResolution.Core/`. UI in Developer Desktop. |
| **Edit Session** | Server-side tracked editing session on project changes. `Rainier.Server.Core/Domain/ProjectChanges/EditSession.cs`. |
| **Governed Edit** | A model edit that must go through `Rainier.Governance` (not direct file writes) — e.g., metadata, calc-engine-version upgrade, bulk projection setting edits. Gated by `Permissions.Authoring.Edit`. |
| **LAPA / Copilot Chat** | AI chatbot inside Integrate. Gated by `EnableLapaChatbot` toggle. Component: `apps/web/src/components/copilot-chat/`. |
| **Storybook** | Component visual catalog (`monorepo/desktop/ui/tools/storybook/`), published to `https://ltssharedassets.z13.web.core.windows.net/`. |
| **ESG** | Economic Scenario Generator. Risk-neutral scenarios delivered as `.bin` files in `monorepo/risk-neutral-esg/`, quarterly. |
| **SDK (Calc Engine SDK)** | A related product line branched off `release/sdk/v*`. `.github/workflows/sync-main-to-sdk.yml` keeps it in sync. Has a dedicated AI agent (`.github/agents/sdk-agent/`). |

## Core entities and relationships

```
User
 ├── has AclPermissions (Owner/Read/Write) on Resource(s)
 └── authors

Project (top-level)
 ├── has many Branch
 │    └── has many Commit
 │         └── may be Published → IFP/IDM
 ├── has many ModelFile (Ain2/Adb2/Atb2/Cmd2/Inforce2) — stored as GDF
 ├── has many EtlJob
 ├── has WorkspaceTemplate (via Governance)
 ├── has Notes (Governance)
 ├── has Metadata (Governance)
 └── has Permissions (ACL)

Job (run)
 ├── belongs to Project / Commit
 ├── has JobStatus (+ events over SignalR)
 ├── has JobResult(s)
 ├── uses CalcEngineVersion
 ├── uses GridUtilsVersion
 └── executes ProjectionRunConfiguration(s)

MgAlfaModel (the in-APL / in-GDF model)
 ├── Ain2 (input/model)
 │    ├── Projections
 │    ├── Database reference → Adb2
 │    └── Scenario reference
 ├── Adb2 (database — formulas, variables)
 │    └── Formula(s)  — parsed/validated by FormulaDb
 ├── Atb2 (tables)
 └── Inforce2 (inforce / seriatim data)

Collaboration (MMD)
 User ↔ DeveloperDesktop ↔ DeveloperSite ↔ other Users
                ↓
           ConflictResolution on GDF nodes
```

## Business rules encoded in code

- **Read access requires an ACL `Read` permission** — enforced in `Rainier.Acl/src/Rainier.Acl/Authorization/PermissionHandler.cs`, policy `"Read"` configured at `Rainier.Acl/.../Startup.cs:98`.
- **Managing model owners is gated** — `AclController.cs:129, :183, :239` require the `ManageModelOwner` claim. The `DisableManageModelOwner` feature toggle (`FeatureToggleConfig.cs`) can turn this off org-wide.
- **Promoting a model to IFP requires `PromoteModelToIfp`** — policy defined in `Rainier.ProjectManager.Server/Startup.cs:73-74`, enforced in `CommitPublishController.cs`.
- **Governance mutations require `Permissions.Authoring.Edit`** — `[RequiresPermission(Permissions.Authoring.Edit)]` on `GovernanceController.cs:20`. No direct model writes happen through the public API.
- **Offline / disconnected mode** — When `UseOfflineConnection` feature toggle is on, the local services use `AddJwtOfflineLifetime()` (issue-once-per-session), and the desktop servers don't require fresh tokens for subsequent calls.
- **Calc engine version selection is gated** — `PreReleaseCalcEngines` toggle controls visibility of pre-release calc engine versions in RunManager's `CompatibleCalcEngineVersions` response.
- **Feature toggles are sectioned** — `feature-toggles.json` has three sections (default-false, default-true, default-"internal"); toggles with value `"internal"` become true only when the master `Internal` toggle is true, which QA/dev should enable per `README.md:235`.
- **Feature toggles are synthesized at build time** — toggles defined in `app-toggles.ts` are bundled with the installer; toggles not in `app-toggles.ts` are stripped from `feature-toggles.json` on start. This enforces that new flags must be committed.
- **Verbose logging is runtime-toggleable** — `VerboseLogging` in `FeatureToggleConfig` raises Serilog level via `LoggingLevelSwitch` at `Startup.Configure` in PM/RM/Gov.
- **HTTP clients are Polly-wrapped** — `Rainier.ProjectManager.Server/Startup.cs:199-215` applies circuit-breaker + retry + fallback for cloud MMD and ACL calls, meaning clients survive transient outages.
- **Three execution modes for projections** — The same `StartProjectionsCommand` can land on local AHost, grid (via GridUtils), or cloud (via Origin SAS), selected per job by configuration. `grid-utils-integration/README` documents the dispatch matrix.
- **Merge conflicts go through DeveloperDesktop** — `SyncMergeCommand` in ProjectManager hands off to DD for user resolution; a `DebugSyncMergeClient` flag in `Infrastructure/Configuration/CloudConfig.cs` lets developers run a local DD build.
- **Automation-only delete** — `AutomationOnlyDelete` feature toggle (`FeatureToggleConfig.cs`) restricts destructive delete operations to automation identities.
- **GDF files have versioned schema upgrades** — `GdfWith_Format_UpgradeSteps.cs` applies ordered upgrades when loading an older-format file, keeping reads backward compatible.
- **Released calc engines are bundled** — `src/server/Rainier.RunManager/CalcEngine-bundles/CalcEngine-v11.1.csproj` (and similar) pin calc engine versions per app release.
- **Feature toggles surfaces to Copilot too** — `EnableLapaChatbot` guards the in-app AI assistant (`apps/web/src/app.tsx:~`).
