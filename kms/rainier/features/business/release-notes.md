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
