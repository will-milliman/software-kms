# GitHub Release Automation

- **Category:** dev
- **Status:** active
- **Owners:** devex / build engineering

## Summary

Containerized tools and GitHub workflows that automate tagging builds, creating GitHub releases, and opening PRs that update release notes.

## Behavior

- A `tag-build` workflow tags the commit corresponding to a successful release pipeline.
- A `release-notes-pr` workflow runs the AI release-note drafter and opens/updates a PR against the release branch.
- Tools run in a published container image so behavior is reproducible locally and in CI.

## Entry Points

- `.github/workflows/tag-build.yml`, `.github/workflows/release-notes-pr.yml` (names approximate).
- Supporting tools under `src/tools/` or `monorepo/tools/`.

## Key Dependencies

- [Release Note Generation](release-note-generation.md).
- [Versioning Pipelines](versioning-pipelines.md).

## Related Features

- [Main → SDK Branch Sync](main-to-sdk-sync.md)

## Change Log

- 2026-04-21: Seeded.
