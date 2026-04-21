# Theme Builder

- **Category:** business
- **Status:** active
- **Owners:** UI Platform Team (sandbox)

## Summary

An interactive pane inside `sandbox-01` that lets a developer search, edit, and preview Carbon/Milliman theme tokens in real time, then export the result in multiple formats.

## Behavior

- Opens as a resource tab (via SideNav in sandbox-01) with a token search box, grouped token list, and a live preview panel.
- Editing a token immediately updates the preview via a dedicated `theme-preview-context`.
- Exports the edited token set via an Export Panel / Export Modal in multiple formats (see `export-formats.ts`) — e.g., JSON, SCSS, CSS custom properties.
- Uses the same `GlobalThemeProvider` infrastructure as the rest of the app, so toggling light/dark still works while designing.

## Entry Points

- `apps/web/sandbox-01/src/theme-builder/index.ts:1-3` — barrel export.
- `apps/web/sandbox-01/src/theme-builder/theme-builder-panel.tsx` — main panel.
- `apps/web/sandbox-01/src/theme-builder/use-theme-builder.ts` — state hook.
- `apps/web/sandbox-01/src/theme-builder/theme-tokens.ts` — token catalog.
- `apps/web/sandbox-01/src/theme-builder/theme-preview-context.ts` — preview subscription.
- `apps/web/sandbox-01/src/theme-builder/components/` — `token-search`, `token-group`, `token-editor`, `theme-preview`, `export-panel`, `export-modal`.
- `apps/web/sandbox-01/src/theme-builder/export-formats.ts` — supported export formats.
- Unit tests: `apps/web/sandbox-01/src/theme-builder/*.test.tsx` (+ component specs).

## Key Dependencies

- `@carbon/themes`, `@carbon/styles` — source tokens.
- `@integrate-core-ui/themes` — `GlobalThemeProvider` + `useGlobalTheme` for live preview.
- `@integrate-core-ui/modal` — Export Modal uses the Modal Service.

## Related Features

- [Theme Toggle](./theme-toggle.md)
- [Theme Providers (dev)](../dev/theme-providers.md)
- [Sandbox Apps (dev)](../dev/sandbox-apps.md)

## Change Log

- 2026-04-21: Seeded.
