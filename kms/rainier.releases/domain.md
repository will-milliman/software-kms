# Domain Model

## Glossary

- **Integrate app** — the Rainier product whose releases this repo governs. Composed of a client tier (Electron desktop app) and a server tier (Origin API, ACL, Initializers, Developer Site, Developer Desktop, GridUtils).
- **Release candidate (RC)** — a specific upstream CI build selected for release. Once approved, the build is tagged `ApprovedReleaseCandidate` so the Classic Release engine can discover it.
- **Authoring and Governance** — the Milliman team that owns the repo and the "Select Release Candidate" gate. Mapped to ADO group `[Rainier]\Authoring and Governance` and GitHub team `@milliman-lts/authoring-and-governance`.
- **Approvers (Integrate App / QA / Ops)** — ADO groups `[Rainier]\Approvers - Integrate App`, `[Rainier]\Approvers - QA`, `[Rainier]\Approvers - Ops` that must sign off in `InitialApprovals`. Security is gated by `[Rainier]\Security Team`.
- **Pipeline 799** — the ADO pipeline ID associated with this repo's YAML definition ("Integrate App Approvals"). Referenced by downstream release pipelines via the `_Integrate App Approvals` artifact alias.
- **Release body** — the JSON document (`server-release-body.json` or `client-release-body.json`) produced during approval. Its `resources.pipelines.*.version` map is re-used verbatim when triggering the downstream Create-Release pipeline, so the versions approved here are the versions that deploy.
- **Server release** — release scope that ships origin, acl, initializers, developersite, and developerdesktop together. Keyed by `ReleaseType = Server`.
- **Client release** — release scope that ships just electron. Keyed by `ReleaseType = Client`. The two are published and approved independently.
- **Classic Release** — ADO Classic Release Management (as opposed to YAML multi-stage). Downstream of pipeline 799 and sets env vars consumed by `Invoke-IntegrateRelease.ps1` (see below).
- **SendGrid** — third-party transactional email provider used to notify customers that a new Integrate version is available.

## Core entities

### Pipeline resources (upstream CI builds)

| Alias | ADO source | Trigger branches | Trigger tags |
|---|---|---|---|
| `electron_resource` | Rainier.Electron Pipeline (177) | main | IndividualCI |
| `grid_utils_resource` | GridUtils CI Build (196) | main | — |
| `origin_resource` | Rainier.Origin API (225) | main | — |
| `acl_resource` | Rainier.ACL (447) | main | — |
| `initializers_resource` | integrate.Initializers (471) | main | — |
| `developer_site_resource` | Rainier.MMD.DeveloperSite (536) | main | IndividualCI |
| `developer_desktop_resource` | Rainier.MMD.DeveloperDesktop (541) | main | IndividualCI |

### Approval gates

| Stage / Job | ADO approver group | Timeout |
|---|---|---|
| `SelectReleaseCandidate` / `Select_RC` | `[Rainier]\Authoring and Governance` | 30 days (43200 min), reject on timeout |
| `InitialApprovals` / `Dev_Approval` | `[Rainier]\Approvers - Integrate App` | 30 days, reject on timeout |
| `InitialApprovals` / `QA_Approval` | `[Rainier]\Approvers - QA` | 30 days, reject on timeout |
| `InitialApprovals` / `Ops_Approval` | `[Rainier]\Approvers - Ops` | 30 days, reject on timeout |
| `InitialApprovals` / `Security_Approval` | `[Rainier]\Security Team` | 30 days, reject on timeout |

### Email notification recipients (pipeline 799)

- **From:** `millimanltsalerts@milliman.com` (`email-notification-config.json`).
- **To:** `zmg-alfa.operations.alerts@milliman.com`.
- **Bcc:** Milliman individuals in `recipients/release-approvals.json` (Steinbauer, Moravaneni, Jones, Boothby, Patel, Wong, Richman).
- Subject template: `Milliman - Integrate App Release Available (<electron runName>)` (`send-release-notification-email.sh:15`).

### Classic Release environment contract

`Invoke-IntegrateRelease.ps1` consumes these env vars supplied by the Classic Release engine:

| Variable | Purpose |
|---|---|
| `PIPELINERUNBRANCH` | Sets `resources.repositories.self.refName` on the Create-Release pipeline invocation. |
| `CREATERELEASEPIPELINEID` | Target pipeline id to invoke. |
| `PRODUCT` | `templateParameters.product` (e.g. `integrate`). |
| `RELEASE_RELEASENAME` | `templateParameters.releaseName`. |
| `RELEASE_RELEASEID` | `templateParameters.releaseId`. |
| `RELEASE_ENVIRONMENTID` | `templateParameters.releaseEnvironmentId`. |
| `SYSTEM_ARTIFACTSDIRECTORY` | Root for locating the `_Integrate App Approvals` artifact drop. |

## Business rules encoded in code

- **Client and server releases are independent.** Pipeline 799 emits two separate release bodies; downstream Classic Releases pick exactly one via the `ReleaseType` parameter (`Invoke-IntegrateRelease.ps1:63`, validated by `ValidateSet('Server','Client')`).
- **Four-way sign-off is mandatory.** All four approval jobs in `InitialApprovals` must succeed before artifacts are published; the stage is marked `isSkippable: false` (`integrate-release-approvals.yaml:77`).
- **Candidate selection is a prerequisite to approvals.** `InitialApprovals` explicitly `dependsOn: SelectReleaseCandidate` (line 76). Authoring and Governance picks the RC before other reviewers can even see it.
- **Approvals expire aggressively.** 30-day `onTimeout: reject` on every manual validation guarantees stale release candidates fail rather than linger.
- **Versions flow through verbatim.** The JSON authored at approval time is consumed unchanged by the Create-Release pipeline; there is no re-resolution of versions downstream. This is the mechanism that makes "approved == shipped".
- **Tagging signals downstream systems.** `ApprovedReleaseCandidate` is the canonical tag other pipelines filter on; it is only applied after artifact publication succeeds (stage `TagBuild` dependsOn `PublishDeploymentArtifacts`).
- **Customer notification is best-effort.** The `ClientNotification` job runs in parallel with `TagBuild` (both `dependsOn: PublishDeploymentArtifacts`) and uses `continueOnError: true` (`integrate-release-approvals.yaml:214`), so email failures do not block tagging or downstream release creation.
- **Recipient lists are auditable.** Email addresses live in git-tracked JSON rather than pipeline variables, by design (see `ClientNotification/README.md:17`).
- **`send_grid_api_key` is the only required pipeline secret.** Explicit in `ClientNotification/README.md:54`.
