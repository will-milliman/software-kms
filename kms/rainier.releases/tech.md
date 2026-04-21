# Technology Stack

## Languages

- **PowerShell** (~5.7 KB) — `Invoke-IntegrateRelease.ps1` plus inline `pwsh` steps inside the YAML pipeline.
- **Bash/Shell** (~3.5 KB) — `send-release-notification-email.sh`.
- **YAML** — Azure DevOps pipeline definition (`.pipelines/integrate-release-approvals.yaml`).
- **JSON** — configuration files for email notification sender and recipients.

No compiled code, tests, or package manifests are present. The repo is pure IaC / scripts.

## Runtimes & tooling

- **Azure DevOps Pipelines** — primary runtime. Uses both the agentless `server` pool (for `ManualValidation@1` approval tasks and build-tag pwsh steps) and Microsoft-hosted `ubuntu-latest` agents (for artifact publishing and the notification email).
- **PowerShell Core (`pwsh`)** — for the artifact-writing steps and `Invoke-IntegrateRelease.ps1`.
- **Bash + Python 3** — `send-release-notification-email.sh` shells out to `python3` to parse the JSON config files.
- **curl** — used to POST to the SendGrid API.

## External services

- **SendGrid Mail Send API** (`https://api.sendgrid.com/v3/mail/send`) — delivers the customer-facing release notification email. Authenticated with a `send_grid_api_key` ADO secret variable.
- **Azure DevOps pipeline resources** (cross-project `mgalfadev/Rainier`):
  - `Rainier.Electron Pipeline` (definition 177) — client component, trigger tag `IndividualCI`.
  - `GridUtils CI Build` (definition 196).
  - `Rainier.Origin API` (definition 225).
  - `Rainier.ACL` (definition 447).
  - `integrate.Initializers` (definition 471).
  - `Rainier.MMD.DeveloperSite` (definition 536) — trigger tag `IndividualCI`.
  - `Rainier.MMD.DeveloperDesktop` (definition 541) — trigger tag `IndividualCI`.
- **Classic Release engine** — consumes the `_Integrate App Approvals` artifact alias (pipeline id 799) and invokes `Invoke-IntegrateRelease.ps1`.

## Expected downstream assets

- `integrate.Environments/.pipelines/release-process/scripts/Run-Pipeline.ps1` — external helper script invoked by `Invoke-IntegrateRelease.ps1` to POST the pipeline run request. Lives in the `integrate.Environments` repo; resolved via the `-RunPipelineScriptPath` parameter (default relative path).

## Build tooling / CI

- **No CI workflows under `.github/workflows/`.** There is no `.github` directory at the repo root. The repo is consumed by Azure DevOps pipelines, not GitHub Actions.
- **`.pipelines/integrate-release-approvals.yaml`** — the only pipeline definition. Registered in ADO as pipeline **799** ("Integrate App Approvals").
- **Pipeline variables required in ADO UI:**
  - `send_grid_api_key` — secret, used by the notification email step.
  - `$(System.AccessToken)` — supplied automatically by ADO; used by `Invoke-IntegrateRelease.ps1` as `-Pat`.
  - Classic Release engine env vars: `PIPELINERUNBRANCH`, `CREATERELEASEPIPELINEID`, `PRODUCT`, `RELEASE_RELEASENAME`, `RELEASE_RELEASEID`, `RELEASE_ENVIRONMENTID`, `SYSTEM_ARTIFACTSDIRECTORY`.

## Governance

- `CODEOWNERS` requires review from **`@milliman-lts/authoring-and-governance`** and **`@milliman-lts/ops-approvers`** for all paths, with an explicit rule for `/.pipelines/`.
