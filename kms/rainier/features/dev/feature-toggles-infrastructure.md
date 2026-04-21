# Feature Toggles (infrastructure)

- **Category:** dev
- **Status:** active
- **Owners:** frontend-platform-engineering

## Summary

The code behind the user-facing toggle system: `FeatureToggleConfig` (.NET) for backend servers and `AppToggles`/`Features` proxy (TypeScript) for the Electron renderer, plus the JSON-to-config bridge that loads `%appdata%/INTEGRATE/feature-toggles.json` at app start.

## Behavior

- On Electron main-process startup, the toggles JSON is merged with `app-toggles.ts` defaults (new toggles added, obsolete ones dropped) and written back.
- Resulting toggles are injected into the renderer via IPC and exposed as a strongly-typed `Features` proxy.
- The local .NET servers receive toggles via `FeatureToggleConfig`, usually at launch argument or config-file level.
- Branch-gated code paths query these structures rather than reading JSON directly.

## Entry Points

- Frontend defaults: `monorepo/desktop/ui/apps/electron/src/options/feature-toggles/app-toggles.ts`.
- Renderer proxy: `monorepo/desktop/ui/packages/feature-toggles/src/feature-toggles.ts`.
- Backend config type: `Rainier.Core` / `Rainier.Server.Core` — search `FeatureToggleConfig`.
- Root `README.md:222-353` documents mechanics.

## Key Dependencies

- [Dev Flags](dev-flags.md) for mixing bundled + local-dev services.

## Related Features

- [Feature Toggles (user-facing)](../business/feature-toggles.md)

## Change Log

- 2026-04-21: Seeded.
