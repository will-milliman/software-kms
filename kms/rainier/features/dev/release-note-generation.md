# Release Note Generation

- **Category:** dev
- **Status:** active
- **Owners:** devex / product

## Summary

An AI-assisted workflow that drafts customer-facing release notes from merged PRs, work items, and changelog fragments, then opens a PR against the release branch for human review.

## Behavior

- Triggered on demand or by the release pipeline.
- Pulls merged PRs in the version range, invokes a Copilot prompt from [AI Prompts Library](ai-prompts-library.md), and writes a Markdown draft.
- A GitHub workflow commits the draft and opens the PR ([GitHub Release Automation](github-release-automation.md)).

## Entry Points

- `.github/prompts/release-notes*.prompt.md`.
- Associated workflow under `.github/workflows/`.

## Key Dependencies

- [AI Prompts Library](ai-prompts-library.md).
- [GitHub Release Automation](github-release-automation.md).

## Related Features

- [Release Notes (business)](../business/release-notes.md)

## Change Log

- 2026-04-21: Seeded.
