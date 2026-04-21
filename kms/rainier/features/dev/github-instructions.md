# GitHub Instructions (Copilot)

- **Category:** dev
- **Status:** active
- **Owners:** devex

## Summary

`.github/instructions/` — path-scoped Copilot instructions that customize AI behavior when editing specific parts of the repo (e.g. frontend code, release notes).

## Behavior

- Each `*.instructions.md` file declares an `applyTo` glob.
- Copilot applies the merged instructions to any assistant interaction within the matched paths.
- Used to enforce local conventions (naming, lint rules, file layout) and to hint at cross-cutting concerns.

## Entry Points

- `.github/instructions/`.

## Key Dependencies

- Copilot / OpenCode clients.

## Related Features

- [AI Prompts Library](ai-prompts-library.md)
- [SDK AI Agent](sdk-ai-agent.md)

## Change Log

- 2026-04-21: Seeded.
