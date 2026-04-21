# CODEOWNERS

- **Category:** dev
- **Status:** active
- **Owners:** devex

## Summary

`.github/CODEOWNERS` maps paths to the GitHub teams required to review PRs touching those paths. Enforced by branch protection rules on `main` and release branches.

## Behavior

- Per-subtree ownership: MG-ALFA paths → mg-alfa team; desktop UI → desktop-ui team; etc.
- Multiple owners per path are supported; any one of them can approve.
- Overrides via `@milliman-lts/<team>` handles.

## Entry Points

- `.github/CODEOWNERS`.

## Key Dependencies

- GitHub branch protection rules.

## Related Features

- [GitHub Instructions (Copilot)](github-instructions.md)

## Change Log

- 2026-04-21: Seeded.
