# Release Body Artifact Publishing

- **Category:** dev
- **Status:** active
- **Owners:** `@milliman-lts/authoring-and-governance`, `@milliman-lts/ops-approvers`

## Summary

After approvals succeed, inline `pwsh` steps build two JSON artifacts that lock in the exact upstream component versions approved during the run. These artifacts are the contract between pipeline 799 and the downstream Create-Release pipeline.

## Behavior

- Runs on `ubuntu-latest`.
- Produces two artifacts:
  - **`server-release-body`** → `server-release-body.json` with `resources.pipelines.{origin,acl,initializers,developersite,developerdesktop}.version`.
  - **`client-release-body`** → `client-release-body.json` with `resources.pipelines.electron.version`.
- Each `version` value is the `$(resources.pipeline.<alias>.runName)` captured at the moment pipeline 799 was queued, so the version that reviewers approved is the version that deploys — no re-resolution downstream.
- Both artifacts are published with `PublishBuildArtifacts@1` (`publishLocation: Container`) under matching container names.
- GridUtils is intentionally **not** included in the server release body (it flows through Origin's build chain).

## Entry Points

- `.pipelines/integrate-release-approvals.yaml:131` — stage.
- `.pipelines/integrate-release-approvals.yaml:141` — server artifact pwsh step.
- `.pipelines/integrate-release-approvals.yaml:160` — `PublishBuildArtifacts@1` for server.
- `.pipelines/integrate-release-approvals.yaml:167` — client artifact pwsh step.
- `.pipelines/integrate-release-approvals.yaml:182` — `PublishBuildArtifacts@1` for client.

## Key Dependencies

- PowerShell Core (`pwsh`) on the hosted agent.
- Downstream consumer: [Classic Release Dispatcher](./classic-release-dispatcher.md).

## Related Features

- [Integrate App Approval Pipeline](./integrate-app-approval-pipeline.md)
- [Classic Release Dispatcher (`Invoke-IntegrateRelease.ps1`)](./classic-release-dispatcher.md)
- [Upstream CI Pipeline Resource Wiring](./upstream-ci-pipeline-resources.md)

## Change Log

- 2026-04-21: Seeded.
