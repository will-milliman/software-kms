# User Docs Build (PDF)

- **Category:** dev
- **Status:** active
- **Owners:** docs / devex

## Summary

PowerShell-driven build pipeline under `user-docs/` that renders the UserGuide, ScriptingGuide, and ReleaseNotes to PDF for shipment with the Integrate installer.

## Behavior

- Sources are Markdown/Word/DITA (depending on doc); PowerShell orchestrates conversion via pandoc/Word automation/etc.
- Output PDFs are placed under a dist folder consumed by the Integrate packaging step.
- Pipeline runs on every release build and on docs PRs.

## Entry Points

- `user-docs/` tree plus associated pipeline YAML.

## Key Dependencies

- [CI Pipelines (Azure DevOps)](ci-pipelines.md).

## Related Features

- [User Docs (PDFs)](../business/user-docs.md)
- [Release Note Generation](release-note-generation.md)

## Change Log

- 2026-04-21: Seeded.
