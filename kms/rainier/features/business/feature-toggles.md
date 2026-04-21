# Feature Toggles (user-facing)

- **Category:** business
- **Status:** active
- **Owners:** frontend-platform-engineering

## Summary

A runtime flag system users (especially QA and internal staff) can edit to enable/disable features. Stored in `%appdata%/INTEGRATE/feature-toggles.json`. Three sections: defaults-false, defaults-true, defaults-"internal".

## Behavior

- On startup, Integrate reads `feature-toggles.json` and merges with `app-toggles.ts` (dropping obsolete toggles, adding new defaults).
- The `Internal` master toggle in section 1 promotes all `"internal"`-valued toggles to true.
- Restarting the app is required to pick up changes.
- User-toggleable toggles (`userToggle: true`) can be changed via UI; non-user toggles still exist but are admin/dev-only.

## Entry Points

- File: `%appdata%/INTEGRATE/feature-toggles.json` (on each user's machine).
- Definitions: `monorepo/desktop/ui/apps/electron/src/options/feature-toggles/app-toggles.ts:26-291` — ~40 `ToggleItem` entries.
- UI access (renderer): `monorepo/desktop/ui/packages/feature-toggles/src/feature-toggles.ts:53-76` — `Features` proxy.
- Docs: root `README.md:222-269, :326-353`.

## Key Dependencies

- [Feature Toggles (infrastructure)](../dev/feature-toggles-infrastructure.md) — dev counterpart.

## Related Features

- [In-app AI Assistant (LAPA Copilot Chat)](in-app-ai-assistant.md)
- [ETL Authoring](etl-authoring.md)

## Change Log

- 2026-04-21: Seeded.
