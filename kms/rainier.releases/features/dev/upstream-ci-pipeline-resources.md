# Upstream CI Pipeline Resource Wiring

- **Category:** dev
- **Status:** active
- **Owners:** `@milliman-lts/authoring-and-governance`, `@milliman-lts/ops-approvers`

## Summary

The `resources.pipelines` block declares the seven Rainier component CI pipelines that both trigger an approval run **and** supply the `version` values baked into the release-body artifacts.

## Behavior

- Each entry specifies `pipeline: <alias>`, `source: <ADO pipeline name>`, and a `trigger` block with `branches: [main]` and optionally `tags: [IndividualCI]`.
- Alias is what the YAML references via `$(resources.pipeline.<alias>.runName)` (e.g. in the release-body pwsh steps and the pipeline run name).
- Client-side components (`electron_resource`, `developer_site_resource`, `developer_desktop_resource`) require the `IndividualCI` tag to trigger; server-side components (`grid_utils_resource`, `origin_resource`, `acl_resource`, `initializers_resource`) trigger on any main-branch build.
- The full mapping is documented in `domain.md` → "Pipeline resources".
- Source pipelines live in the cross-project ADO project `mgalfadev/Rainier`; definition IDs are captured as YAML comments next to each entry.

## Entry Points

- `.pipelines/integrate-release-approvals.yaml:9` — `resources:` block.
- `.pipelines/integrate-release-approvals.yaml:11` — `electron_resource`.
- `.pipelines/integrate-release-approvals.yaml:18` — `grid_utils_resource`.
- `.pipelines/integrate-release-approvals.yaml:23` — `origin_resource`.
- `.pipelines/integrate-release-approvals.yaml:28` — `acl_resource`.
- `.pipelines/integrate-release-approvals.yaml:33` — `initializers_resource`.
- `.pipelines/integrate-release-approvals.yaml:38` — `developer_site_resource`.
- `.pipelines/integrate-release-approvals.yaml:45` — `developer_desktop_resource`.

## Key Dependencies

- ADO `resources.pipelines` cross-project reference to `mgalfadev/Rainier`.
- Upstream pipelines must tag client builds with `IndividualCI` for triggering to work.

## Related Features

- [Integrate App Approval Pipeline](./integrate-app-approval-pipeline.md)
- [Release Body Artifact Publishing](./release-body-artifact-publishing.md)

## Change Log

- 2026-04-21: Seeded.
