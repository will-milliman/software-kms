# ApprovedReleaseCandidate Build Tag

- **Category:** dev
- **Status:** active
- **Owners:** `@milliman-lts/authoring-and-governance`, `@milliman-lts/ops-approvers`

## Summary

After artifacts publish successfully, the `TagBuild` stage writes the `ApprovedReleaseCandidate` build tag onto the pipeline 799 run. Downstream Classic Releases filter on this tag to find approved candidates.

## Behavior

- Runs on the `ubuntu-latest` Microsoft-hosted agent pool (previously `server`; switched in PR #11 so the script can execute).
- Single pwsh step: `Write-Host "##vso[build.addbuildtag]ApprovedReleaseCandidate"`.
- Depends on `PublishDeploymentArtifacts` with `condition: succeeded()` — tagging only happens if the artifacts actually exist, which protects downstream consumers.

## Entry Points

- `.pipelines/integrate-release-approvals.yaml:189` — `TagBuild` stage.
- `.pipelines/integrate-release-approvals.yaml:199` — tag-emitting `pwsh` command.

## Key Dependencies

- ADO logging command `##vso[build.addbuildtag]`.

## Related Features

- [Integrate App Approval Pipeline](./integrate-app-approval-pipeline.md)
- [Release Body Artifact Publishing](./release-body-artifact-publishing.md)

## Change Log

- 2026-04-21: Seeded.
- 2026-04-21: PR #11 Switch integrate-release-approvals pipeline job to ubuntu-latest pool (AB#91350) — `TagBuild` stage now runs on `ubuntu-latest` (was agentless `server`) so the tag-emitting pwsh script can execute.
- 2026-04-22: PR #13 Log all component versions before approvals (AB#91392) — `TagBuild` marked `isSkippable: false`.
