# Release Notes

- **Category:** business
- **Status:** active
- **Owners:** authoring-and-governance, documentation-approvers

## Summary

Customer-facing release notes surfaced inside Integrate (release notes view) and also published as part of the user-docs PDFs.

## Behavior

- Per-release, changes are summarized in customer-facing language.
- The app surfaces release notes via a dedicated renderer component (e.g., post-update "What's new").
- Release notes follow a tech-writer style guide enforced via `.github/instructions/release-note.instructions.md`.

## Entry Points

- UI:
  - `monorepo/desktop/ui/apps/web/src/components/release-notes/`.
  - `monorepo/desktop/ui/apps/web/src/components/upgrade-notification/`.
- Source content: `user-docs/Release Notes/`.
- Style instruction (Copilot-scoped): `.github/instructions/release-note.instructions.md`.
- Scoped copy for user-docs: `user-docs/.github/instructions/release-note.instructions.md`.

## Key Dependencies

- [Release Note Generation (dev)](../dev/release-note-generation.md) — authoring workflow.
- [User Docs](user-docs.md).

## Related Features

- [User Docs](user-docs.md)
- [Release & Promote (IFP/IDM)](release-and-promote.md)

## Change Log

- 2026-04-21: Seeded.
- 2026-05-07: PR #9619 Manually create release notes section for 4.106 release — added the 4.106 release notes section.
- 2026-05-07: PR #9681 v0.4 Release Notes — added SDK v0.4 release notes.
- 2026-05-07: PR #9694 Update release notes for 2026.4.154 (202605.12) — updated release notes for build 2026.4.154.
- 2026-05-07: PR #9715 chore: make release notes folder look like other doc folders — reorganized the release notes folder to match other documentation folders.
