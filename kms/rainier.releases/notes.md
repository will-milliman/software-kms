# Notes

Append-only change log maintained by `/pull`.

- 2026-04-21: PR #11 Switch integrate-release-approvals pipeline job to ubuntu-latest pool (spencerjones-millimanlts, AB#91350) — `TagBuild` stage moved from agentless `server` pool to `ubuntu-latest`.
- 2026-04-21: PR #12 Fix artifact alias and script path resolution in Invoke-IntegrateRelease (spencerjones-millimanlts, AB#91079) — corrected artifact alias to `Integrate App Approvals`; runtime-resolves `-RunPipelineScriptPath` via `$env:SYSTEM_ARTIFACTSDIRECTORY`; added terminating errors for missing env var.
- 2026-04-22: PR #13 Log all component versions before approvals (spencerjones-millimanlts, AB#91392) — added `LogComponentVersions` stage; marked `SelectReleaseCandidate`, `PublishDeploymentArtifacts`, `TagBuild`, and `ClientNotification` as `isSkippable: false`.
