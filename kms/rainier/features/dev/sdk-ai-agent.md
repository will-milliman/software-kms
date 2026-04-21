# SDK AI Agent

- **Category:** dev
- **Status:** active
- **Owners:** calc engine / devex

## Summary

`.github/agents/sdk-agent/` is a multi-file Copilot agent specification that teaches an AI assistant the conventions, APIs, and guardrails of the Calc Engine SDK so it can help external SDK consumers and internal maintainers.

## Behavior

- Agent spec includes persona, tools, restricted commands, and indexed knowledge.
- Paired with the SDK release branch flow ([Main → SDK Branch Sync](main-to-sdk-sync.md)).
- Extensible via [Skill Creator Meta-skill](skill-creator.md) to add new skills.

## Entry Points

- `.github/agents/sdk-agent/`.

## Key Dependencies

- [AI Prompts Library](ai-prompts-library.md).
- [GitHub Instructions (Copilot)](github-instructions.md).

## Related Features

- [Main → SDK Branch Sync](main-to-sdk-sync.md)

## Change Log

- 2026-04-21: Seeded.
