# Skill Creator Meta-skill

- **Category:** dev
- **Status:** active
- **Owners:** devex

## Summary

A meta-skill under `.github/skills/` that guides an AI agent through authoring a new Copilot/OpenCode skill for this repo — scaffolding the `SKILL.md`, associated scripts, and documentation.

## Behavior

- Walks the author through name, trigger, inputs/outputs, and file layout.
- Enforces repo conventions for skills so they're discoverable by Copilot.
- Produces a ready-to-commit skill folder.

## Entry Points

- `.github/skills/skill-creator/` (approximate).

## Key Dependencies

- [Playwright CLI Skill (AI-assisted)](playwright-cli-skill.md) (example skill).

## Related Features

- [GitHub Instructions (Copilot)](github-instructions.md)
- [AI Prompts Library](ai-prompts-library.md)

## Change Log

- 2026-04-21: Seeded.
