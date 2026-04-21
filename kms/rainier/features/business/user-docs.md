# User Docs (PDFs)

- **Category:** business
- **Status:** active
- **Owners:** authoring-and-governance, documentation-approvers

## Summary

Customer-facing documentation source — User Guide, Scripting Guide, and Release Notes — compiled to PDF and shipped with the product.

## Behavior

- Source Markdown/HTML is authored in `user-docs/`.
- A PowerShell build (`GeneratePdf.ps1`) drives NuGet-provided tooling to render PDFs.
- Pipelines publish to downstream feeds / artifact stores.

## Entry Points

- Sources:
  - `user-docs/UserGuide/`.
  - `user-docs/ScriptingGuide/`.
  - `user-docs/Release Notes/`.
- Build:
  - `user-docs/GeneratePdf.ps1`.
  - `user-docs/DocModule.psm1`.
  - `user-docs/packages.config`.
- Pipelines:
  - `user-docs/.pipelines/docs-build.yml`.
  - `user-docs/.pipelines/integrate-app-docs.yml`.
- Tech-writer persona instructions: `user-docs/.github/instructions/release-note.instructions.md`.

## Key Dependencies

- NuGet packages referenced in `user-docs/packages.config`.
- Root `.github/instructions/release-note.instructions.md` rules for release note style.

## Related Features

- [Release Notes](release-notes.md)
- [User Docs Build (dev)](../dev/user-docs-build.md)

## Change Log

- 2026-04-21: Seeded.
