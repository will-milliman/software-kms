# Customer Release Announcement Email

- **Category:** business
- **Status:** active
- **Owners:** `@milliman-lts/authoring-and-governance`, `@milliman-lts/ops-approvers`

## Summary

When a new Integrate app version is approved for release, the approval pipeline sends a SendGrid email to Milliman operations (and a BCC list of stakeholders) announcing availability of the new Electron client version and pointing customers to the admin portal for release notes and the support portal for help.

## Behavior

- Triggered automatically by the `ClientNotification` stage of the Integrate App Approvals pipeline after all four approvals succeed and artifacts are published.
- **From:** `millimanltsalerts@milliman.com`.
- **To:** `zmg-alfa.operations.alerts@milliman.com`.
- **Bcc:** a maintained list of Milliman stakeholders (see `recipients/release-approvals.json`).
- **Subject:** `Milliman - Integrate App Release Available (<electron version>)` where the version equals the approved `Rainier.Electron` run name.
- **Body (plain text):** addressed to "Dear Integrate Customer", announces the new version, directs to the admin portal for release notes and the support portal / `support@integrate-solutions.com` for help, signed by "Milliman Life Technology Solutions Cloud Service Desk".
- Delivery failures do **not** block the release (`continueOnError: true`), but non-2xx responses surface the SendGrid error payload in the job log.

## Entry Points

- `.pipelines/integrate-release-approvals.yaml:202` — `ClientNotification` stage.
- `.pipelines/integrate-release-approvals.yaml:212` — Bash task that invokes the script.
- `.pipelines/scripts/ClientNotification/send-release-notification-email.sh:62` — email body template.

## Key Dependencies

- **SendGrid v3 Mail Send API** — delivery transport.
- `send_grid_api_key` ADO secret variable — bearer token.
- `email-notification-config.json` — `email_from` address (audit-tracked).
- `recipients/release-approvals.json` — `email_to` / `email_bcc` (audit-tracked).

## Related Features

- [Client Release Notification Script (dev view)](../dev/client-release-notification-script.md)
- [Integrate App Approval Pipeline](../dev/integrate-app-approval-pipeline.md)
- [Release Body Artifact Publishing](../dev/release-body-artifact-publishing.md)

## Change Log

- 2026-04-21: Seeded.
