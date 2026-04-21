# Copilot Agents & Skills

- **Category:** dev
- **Status:** active
- **Owners:** Platform Engineering (LTS) / Developer Experience

## Summary

The repo ships with a full GitHub Copilot / OpenCode agent configuration — an `AGENTS.md`, per-persona agent definitions, reusable skills, prompts, and instructions — so AI-assisted work on traffic management follows consistent patterns and guardrails.

## Behavior

- Top-level `AGENTS.md` — the entry guide any agent should read first.
- `.github/copilot-instructions.md` — rules Copilot applies across the repo.
- `.github/copilot-sources.md` — allowed reference sources.
- `.github/agents/*.agent.md` — 5 persona agents (e.g. terraform reviewer, python reviewer, runbook author).
- `.github/skills/*/SKILL.md` — 14 reusable skills (e.g. `terraform-plan-triage`, `function-deploy-debug`).
- `.github/prompts/*.prompt.md` — task-specific prompts.
- `.github/instructions/*.instructions.md` — scoped instructions (e.g. commit-message style, PR body style).
- `.github/CODEOWNERS` — review ownership.

## Entry Points

- `AGENTS.md`.
- `.github/copilot-instructions.md`, `.github/copilot-sources.md`.
- `.github/agents/`, `.github/skills/`, `.github/prompts/`, `.github/instructions/`.
- `.github/CODEOWNERS`.

## Key Dependencies

- GitHub Copilot / OpenCode-compatible agent runtime.

## Related Features

- [Azure DevOps OIDC Pipelines](azure-devops-oidc-pipelines.md)

## Change Log

- 2026-04-21: Seeded.
