# Toolbox

- **Category:** business
- **Status:** active
- **Owners:** UI Platform Team

## Summary

A collapsible side panel that exposes contextual "tools" for the currently active resource. Apps register tools via `useToolbox().addTool(...)`; the Toolbox renders them as Carbon tabs/lists with pin, visibility, and multi-column support.

## Behavior

- A `ToggleToolbox` button in the Header or shell collapses/expands the Toolbox.
- Tools are scoped to the active resource — switching resources can swap the tool list (sandbox-01 demonstrates this pattern at `src/app.tsx:69-121`).
- A `ToolContext` provided to each tool's subtree (via `useTool()`) exposes the active tool.
- Tools support pin, visibility toggling, and container indexing for multi-column layouts; state is driven by `createTabContext` in `@integrate-core-ui/store`.
- The split between content region and toolbox is resizable via `react-resizable-panels`.
- `Toolbox` and `ToolboxTabs` use a proper flex layout with a fixed viewport height so individual tab panels scroll independently.
- Non-visible toolbox tabs stay mounted (hidden) so their effects continue to run; guards skip rendering when the active resource is undefined.

## Entry Points

- `packages/frame/src/toolbox/` — `Toolbox`, `ToolboxTabs`, `ToolboxList`.
- `packages/frame/src/toggle-toolbox/` — `ToggleToolbox`.
- `packages/store/src/contexts/toolbox.context.tsx` — `ToolboxProvider`, `useToolbox`, `ToolContext`, `useTool`.
- Reference: `apps/web/sandbox-tenant-manager/src/pages/roles/roles.page.tsx:15` (Add/Delete Role tools injected into the Toolbox).

## Key Dependencies

- `@carbon/react` — Tabs, buttons, icons.
- `@integrate-core-ui/store` — state management.
- `react-resizable-panels` — split pane.

## Related Features

- [App Shell Frame](./app-shell-frame.md)
- [Tabbed Resources](./tabbed-resources.md)
- [Store Contexts (dev)](../dev/store-contexts.md)

## Change Log

- 2026-04-21: Seeded.
- 2026-04-23: PR #167 Apply Milliman theme refinements and stabilize toolbox layout — `Toolbox`/`ToolboxTabs` reworked to flex layout with fixed viewport height; non-visible tabs stay mounted so effects still run; `@integrate-core-ui/frame` bumped to 7.1.0.
