# Feature Catalog — rainier.releases

## Business features

| Feature | Summary |
|---|---|
| [Customer Release Announcement Email](business/customer-release-announcement-email.md) | Sends an Integrate app release-available email to customer operations via SendGrid as part of the approval pipeline. |

## Dev features

| Feature | Summary |
|---|---|
| [Integrate App Approval Pipeline](dev/integrate-app-approval-pipeline.md) | ADO YAML pipeline (799) that gates Integrate releases behind Dev/QA/Ops/Security manual approvals. |
| [Release Candidate Selection Gate](dev/release-candidate-selection-gate.md) | First-stage manual validation by Authoring and Governance that picks which RC advances. |
| [Release Body Artifact Publishing](dev/release-body-artifact-publishing.md) | Emits `server-release-body.json` / `client-release-body.json` that lock in versions at approval time. |
| [ApprovedReleaseCandidate Build Tag](dev/approved-release-candidate-tag.md) | Post-approval `##vso[build.addbuildtag]` that lets downstream Classic Releases discover approved runs. |
| [Classic Release Dispatcher (`Invoke-IntegrateRelease.ps1`)](dev/classic-release-dispatcher.md) | PowerShell entry point that merges Classic Release context into the approved release body and triggers the Create-Release pipeline. |
| [Client Release Notification Script](dev/client-release-notification-script.md) | Developer-facing internals of the SendGrid bash script, recipient JSON files, and pipeline wiring. Paired with the business feature of the same name. |
| [Upstream CI Pipeline Resource Wiring](dev/upstream-ci-pipeline-resources.md) | Declares the seven Rainier component pipelines that trigger approvals and populate release-body versions. |
| [CODEOWNERS Governance](dev/codeowners-governance.md) | Enforces Authoring-and-Governance + Ops-Approvers review on all changes, with an explicit rule for `/.pipelines/`. |

## Straddling features

- **Client Release Notification** spans both categories:
  - Business view → [business/customer-release-announcement-email.md](business/customer-release-announcement-email.md) (customer-visible email).
  - Dev view → [dev/client-release-notification-script.md](dev/client-release-notification-script.md) (script, SendGrid integration, config files).
