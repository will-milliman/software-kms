# In-app AI Assistant (LAPA Copilot Chat)

- **Category:** business
- **Status:** active (flag-gated)
- **Owners:** frontend-platform-engineering

## Summary

An integrated chat assistant ("LAPA") inside the Integrate web renderer. Controlled by the `EnableLapaChatbot` feature toggle.

## Behavior

- When `Features.EnableLapaChatbot` is true (via feature-toggle file), a chat UI appears.
- Users can ask natural-language questions about Integrate features, projects, or models.
- Disabled by default in `feature-toggles.json` until rollout.

## Entry Points

- UI: `monorepo/desktop/ui/apps/web/src/components/copilot-chat/`.
- Mount point: `monorepo/desktop/ui/apps/web/src/app.tsx:61` — conditional on `Features.EnableLapaChatbot`.
- Toggle definition: `monorepo/desktop/ui/apps/electron/src/options/feature-toggles/app-toggles.ts:26-291` — `EnableLapaChatbot` entry.

## Key Dependencies

- `@repo/feature-toggles`.
- External chat/assistant backend (not in this repo).

## Related Features

- [Feature Toggles](feature-toggles.md)

## Change Log

- 2026-04-21: Seeded.
