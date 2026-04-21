# Architecture

## Component overview

```mermaid
flowchart TD
    subgraph Upstream["Upstream CI builds (mgalfadev/Rainier)"]
        E[Rainier.Electron 177]
        G[GridUtils 196]
        O[Rainier.Origin API 225]
        A[Rainier.ACL 447]
        I[integrate.Initializers 471]
        DS[Rainier.MMD.DeveloperSite 536]
        DD[Rainier.MMD.DeveloperDesktop 541]
    end

    Upstream --> P799[["Pipeline 799<br/>integrate-release-approvals.yaml"]]

    P799 --> S1[SelectReleaseCandidate<br/>ManualValidation]
    S1 --> S2[InitialApprovals<br/>Dev + QA + Ops + Security]
    S2 --> S3[PublishDeploymentArtifacts<br/>server-release-body.json<br/>client-release-body.json]
    S3 --> S4[TagBuild<br/>ApprovedReleaseCandidate]
    S3 --> S5[ClientNotification<br/>SendGrid email]

    S3 -.artifact.-> CR[Classic Release pipeline]
    CR --> IIR[Invoke-IntegrateRelease.ps1]
    IIR --> CRP[[Create-Release pipeline<br/>$CREATERELEASEPIPELINEID]]
```

## Entry points

- **`.pipelines/integrate-release-approvals.yaml:1`** — root ADO pipeline definition (pipeline 799). Defines `resources.pipelines` for all upstream CI builds and all stages below.
- **`.pipelines/integrate-release-approvals.yaml:57`** — `SelectReleaseCandidate` stage; first `ManualValidation@1` gate for Authoring and Governance to pick the release candidate.
- **`.pipelines/integrate-release-approvals.yaml:74`** — `InitialApprovals` stage with four parallel manual-validation jobs: Dev, QA, Ops, Security (lines 79, 92, 105, 118).
- **`.pipelines/integrate-release-approvals.yaml:131`** — `PublishDeploymentArtifacts` stage; inline pwsh composes and publishes the two release-body JSON artifacts (`server-release-body` from line 141, `client-release-body` from line 167).
- **`.pipelines/integrate-release-approvals.yaml:189`** — `TagBuild` stage; writes the `ApprovedReleaseCandidate` build tag (line 199).
- **`.pipelines/integrate-release-approvals.yaml:202`** — `ClientNotification` stage; invokes the Bash notification script (line 212).
- **`.pipelines/scripts/Invoke-IntegrateRelease.ps1:57`** — PowerShell `param(...)` block is the CLI entry; `$PSCmdlet.ShouldProcess(...)` at line 129 triggers the downstream release.
- **`.pipelines/scripts/ClientNotification/send-release-notification-email.sh:1`** — Bash entry point; positional args defined at lines 3–5.

## Data flow

1. **Upstream triggers.** Any tagged `IndividualCI` build of Electron, DeveloperSite, or DeveloperDesktop — or any main-branch build of GridUtils, Origin, ACL, or Initializers — triggers pipeline 799 (`integrate-release-approvals.yaml:9–51`).
2. **Manual gating.** Authoring and Governance accepts or rejects the candidate in `SelectReleaseCandidate` (timeout 30 days, `onTimeout: reject`). Once approved, Dev/QA/Ops/Security approvals run in parallel in `InitialApprovals`. Each `ManualValidation@1` has a `timeoutInMinutes: 43200` (30 days).
3. **Artifact synthesis.** `PublishDeploymentArtifacts` writes two JSON documents containing the resolved upstream `runName` values under `resources.pipelines.*.version`:
   - `server-release-body.json` — origin, acl, initializers, developersite, developerdesktop.
   - `client-release-body.json` — electron.
   Both are published as ADO build artifacts (`PublishBuildArtifacts@1`) with matching container names.
4. **Tagging.** `TagBuild` emits `##vso[build.addbuildtag]ApprovedReleaseCandidate` so downstream Classic Releases can filter to approved runs.
5. **Customer notification.** `ClientNotification` downloads sources and runs `send-release-notification-email.sh`, which:
   - Parses `email-notification-config.json` (from) and `recipients/release-approvals.json` (to/bcc) via `python3 -c`.
   - Constructs a SendGrid `personalizations` JSON payload (`send-release-notification-email.sh:48–59`).
   - POSTs to `https://api.sendgrid.com/v3/mail/send` with the `$(send_grid_api_key)` bearer token (line 74).
   - Non-2xx responses exit 1, but the pipeline sets `continueOnError: true` at `integrate-release-approvals.yaml:214`.
6. **Downstream release dispatch.** A separate Classic Release consumes the approved build, downloads the release-body artifact under the `_Integrate App Approvals` alias, and runs `Invoke-IntegrateRelease.ps1`:
   - Reads the JSON at `$SYSTEM_ARTIFACTSDIRECTORY/_Integrate App Approvals/<type>-release-body/<type>-release-body.json` (`Invoke-IntegrateRelease.ps1:78–82`).
   - Adds `resources.repositories.self.refName = $PIPELINERUNBRANCH` (line 108).
   - Adds `templateParameters` (product, releaseName, releaseId, releaseEnvironmentId) (line 114).
   - Serializes with `ConvertTo-Json -Depth 10` and delegates to `Run-Pipeline.ps1` from the `integrate.Environments` repo (line 130).

## Module / package layout

```
.
├── README.md                                           -- repo landing page
├── CODEOWNERS                                          -- review routing
└── .pipelines/
    ├── .gitkeep
    ├── integrate-release-approvals.yaml                -- ADO pipeline 799
    └── scripts/
        ├── Invoke-IntegrateRelease.ps1                 -- Classic Release dispatcher
        └── ClientNotification/
            ├── README.md                               -- usage docs for the notifier
            ├── send-release-notification-email.sh      -- SendGrid caller
            ├── email-notification-config.json          -- global "from" address
            └── recipients/
                └── release-approvals.json              -- to/bcc for pipeline 799
```

## Cross-references to external repos

- **`integrate.Environments`** — hosts `Run-Pipeline.ps1` (default path `./integrate.Environments/.pipelines/release-process/scripts/Run-Pipeline.ps1`) and is expected to be checked out alongside this repo in the Classic Release. Referenced in `Invoke-IntegrateRelease.ps1:67`.
- **Engineering Foundations lifecycle docs** — linked from the README badge: `milliman-lts/engineering.foundations.github/docs/life-cycle-designations.md`.
