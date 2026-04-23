# Theme Providers (`@integrate-core-ui/themes`)

- **Category:** dev
- **Status:** active
- **Owners:** UI Platform Team

## Summary

Published library that delivers the Milliman-branded Carbon themes (`milliman-white`, `milliman-g100`), global and inline theme providers, themed AG Grid Quartz themes, and shared TypeScript type declarations.

## Behavior

- `GlobalThemeProvider` (alias `IcuGlobalThemeProvider`) wraps the whole app, persists the active theme in `localStorage['milliman-theme']`, and mirrors the choice to `document.documentElement.dataset.carbonTheme` so Carbon CSS variables apply globally. Falls back to `prefers-color-scheme` when nothing is persisted.
- `useGlobalTheme()` exposes `{ theme, setTheme }`.
- `InlineThemeProvider` (alias `IcuInlineThemeProvider`) scopes a subtree to the alternate theme without touching the root.
- `MillimanThemes` enum lists supported themes.
- `AgGridThemeContext` / `AgGridTreeThemeContext` + `useAgGridTheme` / `useAgGridTreeTheme` expose Quartz theme objects (`white`, `whiteTree`, `g100`, `g100Tree`) built via `themeQuartz.withParams({...})` against Carbon CSS variables — grid retheming happens automatically when Carbon theme toggles. Milliman ag-grid themes use `--cds-text-primary` for foreground; the default `AgGridNoRowsOverlay` background uses `--cds-layer`.
- Milliman G100 v2 token refresh applied to `themes.scss`; the legacy `variables.scss` has been removed.
- Publishes a `./icu.css` export (renamed at build time via `rollup.output.assetFileNames`) alongside the JS entry.
- Shared type packages: `CarbonIconName` and the `styled-components` `DefaultTheme` augmentation.

## Entry Points

- `packages/themes/src/index.ts:1-2` — `milliman`, `types`.
- `packages/themes/src/milliman/index.tsx:1-6` — imports `themes.scss` + `@carbon/styles/css/styles.css`.
- `packages/themes/src/milliman/global-theme.provider.tsx:25` — `GlobalThemeProvider`, `GlobalThemeContext`, `useGlobalTheme`.
- `packages/themes/src/milliman/inline-theme.provider.tsx:19` — `InlineThemeProvider`; `MillimanThemes` enum (`:5`).
- `packages/themes/src/milliman/ag-grid.ts:87-91` — theme contexts + hooks; Quartz themes above.
- `packages/themes/src/types/carbon-icon-name.ts`, `.../styled-components.ts` — shared types.
- `packages/themes/vite.config.mts:48` — `icu.css` rename.

## Key Dependencies

- `@carbon/react`, `@carbon/styles`, `@carbon/themes`, `@carbon/icons-react` (peer) — Carbon Design System.
- `@ag-grid-community/theming` (peer) — `themeQuartz.withParams`.
- `sass` — SCSS compilation with `api: 'modern-compiler'`.

## Related Features

- [Theme Toggle](../business/theme-toggle.md)
- [Data Grid](./data-grid.md)
- [Theme Builder](../business/theme-builder.md)

## Change Log

- 2026-04-21: Seeded.
- 2026-04-23: PR #167 Apply Milliman theme refinements and stabilize toolbox layout — Milliman G100 v2 token refresh applied; removed `variables.scss`; ag-grid uses `--cds-text-primary` and `--cds-layer`; `@integrate-core-ui/themes` bumped to 4.0.0 and `@integrate-core-ui/grid` to 2.0.1.
