# Playwright CLI Skill (AI-assisted)

- **Category:** dev
- **Status:** active
- **Owners:** desktop-ui / devex

## Summary

A repo-local Copilot/OpenCode skill (`.github/skills/playwright-cli`) that teaches an AI agent to attach to a *running* Integrate Electron instance via Chrome DevTools Protocol and script interactions, enabling exploratory testing and reproduction steps without touching the test suite.

## Behavior

- The skill documents how to launch Electron with `--remote-debugging-port`, attach Playwright via CDP, and drive windows.
- The AI uses the skill to perform session-style interactions: navigate, click, screenshot, inspect state.
- Intended for debugging and test authoring, not CI execution.

## Entry Points

- `.github/skills/playwright-cli/SKILL.md` (and associated scripts).

## Key Dependencies

- [Playwright E2E Orchestration](playwright-e2e-orchestration.md) (shares Playwright dependency).

## Related Features

- [SDK AI Agent](sdk-ai-agent.md)
- [Skill Creator Meta-skill](skill-creator.md)

## Change Log

- 2026-04-21: Seeded.
