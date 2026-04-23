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
  3. `InitialApprovals` — `isSkippable: false`; four parallel jobs (Dev/QA/Ops/Security) each with 30-day reject-on-timeout.
  4. `PublishDeploymentArtifacts` — `ubuntu-latest`, `isSkippable: false`; two inline `pwsh` scripts synthesize and publish `server-release-body` and `client-release-body`.
  5. `TagBuild` — `ubuntu-latest`, `isSkippable: false`; logs `##vso[build.addbuildtag]ApprovedReleaseCandidate`.
  6. `ClientNotification` — `ubuntu-latest`, `isSkippable: false`; runs the SendGrid bash script with `continueOnError: true`.
- `TagBuild` and `ClientNotification` both depend on `PublishDeploymentArtifacts` so they run in parallel.
- Only one shared variable: `approvalTimeoutDays: 30` (informational; individual jobs set `timeoutInMinutes: 43200`).

## Entry Points

- `.pipelines/integrate-release-approvals.yaml:1` — pipeline root.
- `.pipelines/integrate-release-approvals.yaml:56` — `stages:` block.

## Key Dependencies

- **Azure DevOps agentless `server` pool** — for the `ManualValidation@1` step in `SelectReleaseCandidate`.
- **Microsoft-hosted `ubuntu-latest`** — for component-version logging, artifact publishing, tag emission, and email sending.
- **Seven upstream pipeline resources** (see [Upstream CI Pipeline Resource Wiring](./upstream-ci-pipeline-resources.md)).

## Related Features

- [Release Candidate Selection Gate](./release-candidate-selection-gate.md)
- [Release Body Artifact Publishing](./release-body-artifact-publishing.md)
- [ApprovedReleaseCandidate Build Tag](./approved-release-candidate-tag.md)
- [Client Release Notification Script](./client-release-notification-script.md)

## Change Log

- 2026-04-21: Seeded.
- 2026-04-21: PR #11 Switch integrate-release-approvals pipeline job to ubuntu-latest pool (AB#91350) — `TagBuild` stage moved from agentless `server` pool to `ubuntu-latest` so the tag-emitting script can execute.
- 2026-04-22: PR #13 Log all component versions before approvals (AB#91392) — added `LogComponentVersions` stage that runs before `SelectReleaseCandidate` and prints all seven component pipeline run names; marked `SelectReleaseCandidate`, `PublishDeploymentArtifacts`, `TagBuild`, and `ClientNotification` as `isSkippable: false`.
