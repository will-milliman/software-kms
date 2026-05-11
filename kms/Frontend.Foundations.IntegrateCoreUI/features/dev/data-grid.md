# Data Grid (`@integrate-core-ui/grid`)

- **Category:** dev
- **Status:** active
- **Owners:** UI Platform Team

## Summary

AG Grid Enterprise wrapper themed for Milliman/Carbon, with built-in quick filter, no-rows overlay, and an API context so ancestor/descendant components can access grid APIs imperatively.

## Behavior

- `<AgGrid>` is a memoized `forwardRef` wrapper around `AgGridReact` that:
  - Registers the Milliman Quartz theme from `@integrate-core-ui/themes` via `useAgGridTheme()`.
  - Embeds the AG Grid Enterprise license key at build (`ag-grid.tsx:22`).
  - Exposes the grid APIs through `AgGridApisContext`, so `useAgGridContext()` in descendants can read `columnApi`, `api`, and quick-filter callbacks.
  - Uses `useImperativeHandle` so parent refs see the underlying `AgGridReact` ref.
- `<AgGridQuickFilter>` and `<AgGridNoRowsOverlay>` provide drop-in UX pieces.
- Exports `ag-grid.modules` — the canonical module list consumers should register. The package is on AG Grid v35 unified packages and maps split menu modules plus cell selection for backwards-compatible consumer behavior.

## Entry Points

- `packages/grid/src/index.ts:1-2` — barrel (re-exports `ICellRendererParams`).
- `packages/grid/src/ag-grid/index.ts:1-3` — exports `ag-grid`, types, modules.
- `packages/grid/src/ag-grid/ag-grid.tsx:99` — `AgGrid` component; license key at `:22`.
- `packages/grid/src/use-ag-grid-context/use-ag-grid-context.tsx` — `AgGridApisContext`, `useAgGridContext`.
- `packages/grid/src/ag-grid-quick-filter/ag-grid-quick-filter.tsx` — `AgGridQuickFilter`.
- `packages/grid/src/ag-grid-no-rows-overlay/ag-grid-no-rows-overlay.tsx` — `AgGridNoRowsOverlay`.
- `packages/grid/src/ag-grid/ag-grid.stories.tsx` — Storybook stories.

## Key Dependencies

- `ag-grid-community`, `ag-grid-enterprise`, `ag-grid-react` (peer, v35) — grid core, enterprise modules, and React binding.
- `@carbon/react` (peer).
- `@integrate-core-ui/themes` (peer) — Quartz theme + Carbon variable bridging.

## Related Features

- [Theme Providers](./theme-providers.md)
- [Tenant Management](../business/tenant-management.md) — Groups page uses the grid.

## Change Log

- 2026-04-21: Seeded.
- 2026-05-07: PR #171 Migrate AG Grid from v32.3.9 to v35.2.1 — switched from modular AG Grid packages to v35 unified packages, added CellSelectionModule, split menu modules, type re-exports, and backward-compatible wrapper defaults.
- 2026-05-11: PR #175 Update AG Grid Enterprise license key to v3 — updated the embedded AG Grid license to v3 credentials and bumped `@integrate-core-ui/grid` to 2.1.1.
