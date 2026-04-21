# AI Prompts Library

- **Category:** dev
- **Status:** active
- **Owners:** devex

## Summary

`.github/prompts/` — a curated library of Copilot prompts for recurring engineering tasks: PR creation, release-note drafting, test-plan generation, feature-flag removal, and pipeline failure triage.

## Behavior

- Each prompt is a Markdown file with a front-matter header naming its intended tool/mode.
- Prompts are referenced from workflows (e.g. release notes) and directly by engineers via Copilot Chat `/prompt`.
- Update policy: when a workflow changes, the associated prompt is updated in the same PR.

## Entry Points

- `.github/prompts/`.

## Key Dependencies

- [GitHub Instructions (Copilot)](github-instructions.md).

## Related Features

- [Release Note Generation](release-note-generation.md)
- [SDK AI Agent](sdk-ai-agent.md)

## Change Log

- 2026-04-21: Seeded.
