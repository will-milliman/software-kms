# Theme Toggle

- **Category:** business
- **Status:** active
- **Owners:** UI Platform Team

## Summary

Users can switch between Milliman light (`milliman-white`) and dark (`milliman-g100`) Carbon themes via a toggle in the header. The choice is persisted to `localStorage` and applied globally, including to AG Grid Quartz themes.

## Behavior

- Clicking the theme toggle in the Header swaps Milliman Carbon themes immediately.
- If the user has never toggled, the system preference (`prefers-color-scheme`) determines the initial theme.
- Choice is saved under `localStorage['milliman-theme']` and restored on next visit.
- Carbon CSS custom properties (`--cds-*`) propagate the theme; AG Grid Quartz themes are built against these variables and re-theme automatically.
- Localized theme overrides are possible via `InlineThemeProvider` (e.g., a dark panel inside a light shell).

## Entry Points

- `packages/frame/src/theme-toggle/` — `ThemeToggle` component (consumed by the Header).
- `packages/themes/src/milliman/global-theme.provider.tsx:25` — `GlobalThemeProvider`, `useGlobalTheme`.
- `packages/themes/src/milliman/inline-theme.provider.tsx:19` — `InlineThemeProvider`, `MillimanThemes` enum (`:5`).
- `packages/themes/src/milliman/ag-grid.ts:87` — AG Grid Quartz themes bound to Carbon CSS variables.

## Key Dependencies

- `@carbon/react`, `@carbon/styles`, `@carbon/themes` — the Carbon design system.
- `@ag-grid-community/theming` — `themeQuartz.withParams(...)`.
- Browser APIs — `localStorage`, `matchMedia('(prefers-color-scheme: dark)')`, `document.documentElement.dataset.carbonTheme`.

## Related Features

- [Theme Providers (dev)](../dev/theme-providers.md)
- [Theme Builder](./theme-builder.md)
- [App Shell Frame](./app-shell-frame.md)

## Change Log

- 2026-04-21: Seeded.
