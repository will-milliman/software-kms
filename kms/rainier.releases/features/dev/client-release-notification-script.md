# Client Release Notification Script

- **Category:** dev
- **Status:** active
- **Owners:** `@milliman-lts/authoring-and-governance`, `@milliman-lts/ops-approvers`

## Summary

Developer-facing internals of the release-notification email: the bash script, its JSON config contract, and how it is wired into pipeline 799. Paired with the business feature [Customer Release Announcement Email](../business/customer-release-announcement-email.md).

## Behavior

- `send-release-notification-email.sh` takes three positional args: `RELEASE_VERSION`, `API_KEY`, `RECIPIENTS_CONFIG` (path to a JSON file).
- Reads `email_from` from `email-notification-config.json` adjacent to the script (global config).
- Reads `email_to` (required) and `email_bcc` (optional, may be `""`) from the recipients JSON file.
- Parses JSON with inline `python3 -c` one-liners (no `jq` dependency).
- Builds a SendGrid `personalizations` JSON by tokenizing comma-separated email lists on `IFS=','` and assembling the array manually. `bcc` is only included when non-empty.
- POSTs to `https://api.sendgrid.com/v3/mail/send` with `Authorization: Bearer $API_KEY`.
- Captures the HTTP status via `curl -w "\n%{http_code}\n"` and exits non-zero if the status is outside 200–299.
- Echoes the full outgoing JSON payload for debugging (note: recipient emails are logged).
- Adding a new pipeline → add a new JSON file under `recipients/`. No other code change required.

## Entry Points

- `.pipelines/scripts/ClientNotification/send-release-notification-email.sh:1` — script start.
- `.pipelines/scripts/ClientNotification/send-release-notification-email.sh:10` — global `email_from` read.
- `.pipelines/scripts/ClientNotification/send-release-notification-email.sh:32` — conditional bcc block.
- `.pipelines/scripts/ClientNotification/send-release-notification-email.sh:74` — SendGrid `curl`.
- `.pipelines/scripts/ClientNotification/email-notification-config.json:1` — global config.
- `.pipelines/scripts/ClientNotification/recipients/release-approvals.json:1` — pipeline-799 recipients.
- `.pipelines/scripts/ClientNotification/README.md:1` — usage docs for YAML and Classic Release.
- `.pipelines/integrate-release-approvals.yaml:212` — Bash task that invokes the script.

## Key Dependencies

- `bash`, `python3`, `curl` on `ubuntu-latest`.
- SendGrid v3 Mail Send API.
- Pipeline secret variable `send_grid_api_key`.

## Related Features

- [Customer Release Announcement Email (business view)](../business/customer-release-announcement-email.md)
- [Integrate App Approval Pipeline](./integrate-app-approval-pipeline.md)

## Change Log

- 2026-04-21: Seeded.
