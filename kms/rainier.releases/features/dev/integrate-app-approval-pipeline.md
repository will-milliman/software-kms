# Integrate App Approval Pipeline

- **Category:** dev
- **Status:** active
- **Owners:** `@milliman-lts/authoring-and-governance`, `@milliman-lts/ops-approvers`

## Summary

`integrate-release-approvals.yaml` defines ADO pipeline **799** ("Integrate App Approvals"). It is triggered by upstream Integrate component CI builds and runs the full approve → publish → tag → notify flow, producing the artifacts that downstream Classic Releases consume.

## Behavior

- `trigger: none` + `pr: none` — the pipeline is never triggered by code changes; only by `resources.pipelines` completion events.
- Run name format: `$(Date:yyyyMM).$(Rev:r) (<electron run name>)` so runs are keyed off monthly cadence and the Electron version under approval.
- Stages, in order:
  1. `LogComponentVersions` — `ubuntu-latest`; prints the run names of all seven component pipelines (Integrate App, GridUtils, Origin API, ACL, Initializers, Developer Site, Developer Desktop) for traceability.
  2. `SelectReleaseCandidate` — `server` pool, `isSkippable: false`; single `ManualValidation@1` by Authoring and Governance, 30-day timeout → reject.
  3. `PublishDeploymentArtifacts` — `ubuntu-latest`, `isSkippable: false`; two inline `pwsh` scripts synthesize and publish `server-release-body` and `client-release-body`.
  4. `EvaluateReleasePolicies` — `ubuntu-latest`, `isSkippable: false`; asserts all pipeline resources came from `refs/heads/main` and polls for a successful Developer Site Qualys scan.
  5. `GatherReleaseApprovals` — `isSkippable: false`; Dev/QA/Ops manual approvals always run, while Security approval is required when the Qualys check is missing or unsuccessful.
  6. `TagBuild` — `ubuntu-latest`, `isSkippable: false`; logs `##vso[build.addbuildtag]ApprovedReleaseCandidate`.
  7. `ClientNotification` — `ubuntu-latest`, `isSkippable: false`; runs the SendGrid bash script with `continueOnError: true`.
- Release artifact publishing is consolidated into the approvals flow so downstream stages consume the same generated release body artifacts.
- Approval timeouts are parameterized and evaluated with Azure DevOps runtime expression syntax.
- SendGrid credentials are read from Azure Key Vault into a secret pipeline variable before notification email steps.

## Entry Points

- `.pipelines/integrate-release-approvals.yaml:1` — pipeline root.
- `.pipelines/integrate-release-approvals.yaml:56` — `stages:` block.
- `.pipelines/scripts/Assert-ResourceBranchesAreMain.ps1` — fails the policy stage when any upstream pipeline resource did not originate from `refs/heads/main`.
- `.pipelines/scripts/Assert-QualysScanPassed.ps1` — polls ADO build definition 800 for a Qualys scan matching the Developer Site run and records whether Security approval is needed.

## Key Dependencies

- **Azure DevOps agentless `server` pool** — for the `ManualValidation@1` step in `SelectReleaseCandidate`.
- **Microsoft-hosted `ubuntu-latest`** — for component-version logging, artifact publishing, tag emission, and email sending.
- **Seven upstream pipeline resources** (see [Upstream CI Pipeline Resource Wiring](./upstream-ci-pipeline-resources.md)).
- **Qualys scan pipeline** (ADO build definition 800) — checked before release approvals for Developer Site security validation.

## Related Features

- [Release Candidate Selection Gate](./release-candidate-selection-gate.md)
- [Release Body Artifact Publishing](./release-body-artifact-publishing.md)
- [ApprovedReleaseCandidate Build Tag](./approved-release-candidate-tag.md)
- [Client Release Notification Script](./client-release-notification-script.md)

## Change Log

- 2026-04-21: Seeded.
- 2026-04-21: PR #11 Switch integrate-release-approvals pipeline job to ubuntu-latest pool (AB#91350) — `TagBuild` stage moved from agentless `server` pool to `ubuntu-latest` so the tag-emitting script can execute.
- 2026-04-22: PR #13 Log all component versions before approvals (AB#91392) — added `LogComponentVersions` stage that runs before `SelectReleaseCandidate` and prints all seven component pipeline run names; marked `SelectReleaseCandidate`, `PublishDeploymentArtifacts`, `TagBuild`, and `ClientNotification` as `isSkippable: false`.
- 2026-05-07: PR #15 Add release notes PR stage to approvals pipeline — approvals now consume the Rainier cross-repo `publish-release-notes-pr.yml` template after deployment artifacts are published.
- 2026-05-07: PR #16 Pin GridUtils to 14.0.1132 — approval pipeline pins the GridUtils component version to avoid accidental newer defaults.
- 2026-05-07: PR #17 Reduce approvals timeout and Ensure consistent Grid Utils version handling — approval timeouts and GridUtils version are centralized in variables.
- 2026-05-07: PR #18 Fix syntax for timeouts and increase to 7 days — manual approval timeout evaluation now uses Azure DevOps runtime expression syntax and a 7-day timeout.
- 2026-05-07: PR #19 chore: log components earlier and remove duplicate approvals — component logging and release artifact publishing were consolidated and pipeline resources were pinned to `main`.
- 2026-05-07: PR #21 Fetch SendGrid API key from key vault — release notification pipeline now retrieves the SendGrid API key from Azure Key Vault as a secret variable.
- 2026-05-12: PR #20 feat: add policy enforcement — added release policy evaluation for main-branch resource provenance and Developer Site Qualys scan status before approvals.
