# Release Candidate Selection Gate

- **Category:** dev
- **Status:** active
- **Owners:** `@milliman-lts/authoring-and-governance`

## Summary

The first pipeline stage, `SelectReleaseCandidate`, is a single `ManualValidation@1` approval by the `[Rainier]\Authoring and Governance` ADO group. It gates entry to all downstream approvals, ensuring every in-flight pipeline run has an explicitly chosen release candidate before Dev/QA/Ops/Security are asked to review.

## Behavior

- Runs on the `server` pool with `timeoutInMinutes: 43200` (30 days) and `onTimeout: reject`.
- Approver group: `[Rainier]\Authoring and Governance`.
- Approval instructions shown to approvers: *"Select the appropriate release candidate for deployment and approve to proceed with the release process"*.
- `notifyUsers: ""` — relies on ADO group notification defaults.
- `InitialApprovals` has `dependsOn: SelectReleaseCandidate`, so rejecting here short-circuits the entire run.

## Entry Points

- `.pipelines/integrate-release-approvals.yaml:57` — stage declaration.
- `.pipelines/integrate-release-approvals.yaml:66` — the `ManualValidation@1` task itself.

## Key Dependencies

- ADO group `[Rainier]\Authoring and Governance`.

## Related Features

- [Integrate App Approval Pipeline](./integrate-app-approval-pipeline.md)

## Change Log

- 2026-04-21: Seeded.
