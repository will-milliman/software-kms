# CODEOWNERS Governance

- **Category:** dev
- **Status:** active
- **Owners:** `@milliman-lts/authoring-and-governance`, `@milliman-lts/ops-approvers`

## Summary

Repository-level review routing via `CODEOWNERS` requires approvals from both the Authoring-and-Governance and Ops-Approvers GitHub teams on every PR, with an explicit rule reiterating the requirement for `/.pipelines/`.

## Behavior

- Default rule `*` → `@milliman-lts/authoring-and-governance @milliman-lts/ops-approvers`.
- `/.pipelines/` → same two teams (redundant but explicit, so deleting the default rule doesn't silently weaken pipeline review).
- Combined with GitHub branch protection, this enforces dual-team sign-off on every change to the ADO pipeline definition or release scripts.

## Entry Points

- `CODEOWNERS:5` — default rule.
- `CODEOWNERS:8` — `/.pipelines/` rule.

## Key Dependencies

- GitHub teams `milliman-lts/authoring-and-governance` and `milliman-lts/ops-approvers`.
- Repo branch protection configured to require code-owner review (not in this repo; configured at GitHub level).

## Related Features

- [Integrate App Approval Pipeline](./integrate-app-approval-pipeline.md)

## Change Log

- 2026-04-21: Seeded.
