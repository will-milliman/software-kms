# ApprovedReleaseCandidate Build Tag

- **Category:** dev
- **Status:** active
- **Owners:** `@milliman-lts/authoring-and-governance`, `@milliman-lts/ops-approvers`

## Summary

After artifacts publish successfully, the `TagBuild` stage writes the `ApprovedReleaseCandidate` build tag onto the pipeline 799 run. Downstream Classic Releases filter on this tag to find approved candidates.

## Behavior

- Runs on the `server` pool.
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
