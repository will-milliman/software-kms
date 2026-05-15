# ADRs & How-To Docs

- **Category:** dev
- **Status:** active
- **Owners:** devex / all teams

## Summary

Repo-internal documentation trees: `docs/decisions/` (Architecture Decision Records), `docs/how-to/` (task-oriented guides), and `docs/devex/` (developer experience notes).

## Behavior

- ADRs follow a numbered template (status, context, decision, consequences).
- How-tos are keyed by task ("How to add a feature toggle", "How to add a new local service").
- DevEx notes cover environment setup, troubleshooting, and tooling.

## Entry Points

- `docs/decisions/`, `docs/how-to/`, `docs/devex/`.

## Key Dependencies

- None.

## Related Features

- [User Docs Build (PDF)](user-docs-build.md) — separate, customer-facing.

## Change Log

- 2026-04-21: Seeded.
- 2026-05-11: PR #9718 decision: session expiry UX mitigations — added an ADR for gateway-timeout/session-expiry UX mitigations.
- 2026-05-11: PR #9723 doc: add mmd web deprecation rn — added documentation context for MMD web deprecation release-note messaging.
- 2026-05-11: PR #9725 chore: configure dependabot for monorepo with major/minor split — reorganized Dependabot/developer-experience docs around monorepo update strategy.
- 2026-05-11: PR #9727 fix: remove duplicate ecosystem-directory dependabot configurations — added how-to documentation for managing Dependabot tooling.
- 2026-05-11: PR #9750 chore: add ab link and update pip dependabot grouping and schedule — updated Dependabot architecture docs for pip grouping and scheduling.
- 2026-05-12: PR #9765 fix: ab link adding workflow does not run on PR edit — corrected Dependabot ignore syntax and changed AB-link insertion so the work item stays at the top of edited PR bodies.
- 2026-05-12: PR #9767 fix: try greater than or equal instead of inclusive range — converted Dependabot version caps to `>=` constraints for AutoMapper, MediatR, and FluentAssertions.
- 2026-05-13: PR #9796 feature(dev): allow greater than one directory in dependabot update buckets — updated generated Dependabot config/docs for multi-directory NuGet update buckets and org-level registry configuration.
- 2026-05-15: PR #9766 chore: sync main into release/sdk/v0.5.0 — carried Dependabot architecture and management docs into the SDK release branch.
