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
  1. `SelectReleaseCandidate` — `server` pool, single `ManualValidation@1` by Authoring and Governance, 30-day timeout → reject.
  2. `InitialApprovals` — `isSkippable: false`; four parallel jobs (Dev/QA/Ops/Security) each with 30-day reject-on-timeout.
  3. `PublishDeploymentArtifacts` — `ubuntu-latest`; two inline `pwsh` scripts synthesize and publish `server-release-body` and `client-release-body`.
  4. `TagBuild` — `server` pool; logs `##vso[build.addbuildtag]ApprovedReleaseCandidate`.
  5. `ClientNotification` — `ubuntu-latest`; runs the SendGrid bash script with `continueOnError: true`.
- `TagBuild` and `ClientNotification` both depend on `PublishDeploymentArtifacts` so they run in parallel.
- Only one shared variable: `approvalTimeoutDays: 30` (informational; individual jobs set `timeoutInMinutes: 43200`).

## Entry Points

- `.pipelines/integrate-release-approvals.yaml:1` — pipeline root.
- `.pipelines/integrate-release-approvals.yaml:56` — `stages:` block.

## Key Dependencies

- **Azure DevOps agentless `server` pool** — for `ManualValidation@1` and the tag step.
- **Microsoft-hosted `ubuntu-latest`** — for artifact publishing and email sending.
- **Seven upstream pipeline resources** (see [Upstream CI Pipeline Resource Wiring](./upstream-ci-pipeline-resources.md)).

## Related Features

- [Release Candidate Selection Gate](./release-candidate-selection-gate.md)
- [Release Body Artifact Publishing](./release-body-artifact-publishing.md)
- [ApprovedReleaseCandidate Build Tag](./approved-release-candidate-tag.md)
- [Client Release Notification Script](./client-release-notification-script.md)

## Change Log

- 2026-04-21: Seeded.
