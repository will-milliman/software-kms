# Classic Release Dispatcher (`Invoke-IntegrateRelease.ps1`)

- **Category:** dev
- **Status:** active
- **Owners:** `@milliman-lts/authoring-and-governance`, `@milliman-lts/ops-approvers`

## Summary

`Invoke-IntegrateRelease.ps1` is the PowerShell entry point run by the Classic Release pipeline downstream of pipeline 799. It reads the approved release-body artifact, merges Classic-Release-specific fields (`repositories.self.refName`, `templateParameters`), and invokes the Create-Release pipeline via a helper script from the `integrate.Environments` repo.

## Behavior

- `[CmdletBinding(SupportsShouldProcess)]` — supports `-WhatIf`.
- Required parameters:
  - `-Pat` — pass `$(System.AccessToken)` from the task.
  - `-ReleaseType` — `Server` or `Client` (`ValidateSet`). Determines default artifact path.
- Optional parameters:
  - `-RunPipelineScriptPath` — resolved at runtime via `$env:SYSTEM_ARTIFACTSDIRECTORY` (consistent with `-ReleaseBodyPath`); throws a terminating error if `SYSTEM_ARTIFACTSDIRECTORY` is unset and no explicit path is provided.
  - `-ReleaseBodyPath` — defaults to `$SYSTEM_ARTIFACTSDIRECTORY/Integrate App Approvals/<type>-release-body/<type>-release-body.json`.
- Fails fast with a typed `ErrorRecord` (`MissingReleaseBodyArtifact`, `ObjectNotFound`) if the artifact is missing, including a hint that pipeline 799 may not have completed.
- Adds two properties to the deserialized release body:
  - `resources.repositories.self.refName = $env:PIPELINERUNBRANCH`.
  - `templateParameters = { product, releaseName, releaseId, releaseEnvironmentId }` sourced from Classic Release env vars.
- Re-serializes with `ConvertTo-Json -Depth 10`, echoes the request body to the log, and invokes `Run-Pipeline.ps1 -body $body -pipelineId $env:CREATERELEASEPIPELINEID -pat $Pat` (guarded by `$PSCmdlet.ShouldProcess`).
- Sets `$ErrorActionPreference = 'Stop'` at script scope.

## Entry Points

- `.pipelines/scripts/Invoke-IntegrateRelease.ps1:58` — `param(...)` block.
- `.pipelines/scripts/Invoke-IntegrateRelease.ps1:78` — default artifact path resolution.
- `.pipelines/scripts/Invoke-IntegrateRelease.ps1:89` — missing-artifact error.
- `.pipelines/scripts/Invoke-IntegrateRelease.ps1:108` — merge of `resources.repositories.self`.
- `.pipelines/scripts/Invoke-IntegrateRelease.ps1:114` — merge of `templateParameters`.
- `.pipelines/scripts/Invoke-IntegrateRelease.ps1:129` — dispatch to `Run-Pipeline.ps1`.

## Key Dependencies

- `Run-Pipeline.ps1` from `integrate.Environments` — actually POSTs to ADO to create the pipeline run.
- Classic Release env vars: `PIPELINERUNBRANCH`, `CREATERELEASEPIPELINEID`, `PRODUCT`, `RELEASE_RELEASENAME`, `RELEASE_RELEASEID`, `RELEASE_ENVIRONMENTID`, `SYSTEM_ARTIFACTSDIRECTORY`.
- Artifact alias `Integrate App Approvals` configured on the Classic Release.

## Related Features

- [Release Body Artifact Publishing](./release-body-artifact-publishing.md)
- [ApprovedReleaseCandidate Build Tag](./approved-release-candidate-tag.md)

## Change Log

- 2026-04-21: Seeded.
- 2026-04-21: PR #12 Fix artifact alias and script path resolution in Invoke-IntegrateRelease (AB#91079) — corrected artifact alias from `_Integrate App Approvals` to `Integrate App Approvals`; replaced hardcoded `-RunPipelineScriptPath` default with runtime resolution via `$env:SYSTEM_ARTIFACTSDIRECTORY`; added explicit terminating errors when `SYSTEM_ARTIFACTSDIRECTORY` is unset and no explicit path is provided.
