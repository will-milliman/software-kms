# App Shell Frame

- **Category:** business
- **Status:** active
- **Owners:** UI Platform Team

## Summary

The outer chrome shared by Integrate applications: a Carbon-based `Header`, collapsible `SideNav`, tab-driven `TabbedResources` content area, companion `Toolbox`, Electron `WindowControls`, `HelpMenu`, `ThemeToggle`, `SignOut`, and `VersionCopyright`. Apps compose these into a consistent Integrate look and feel.

## Behavior

- Users see a persistent top header with app navigation, help menu, theme toggle, sign-out, and window controls (min/max/close) on Electron.
- A left-side SideNav exposes major sections (e.g., Projects, Job history, Tenant Manager, Theme Builder in sandbox-01).
- Selecting a side-nav item opens the corresponding resource in the Tabbed Resources area; users can switch, pin, and close tabs.
- A Toolbox panel on the right shows tools relevant to the active resource; users can collapse it or drag the split via `react-resizable-panels`.

## Entry Points

- `packages/frame/src/index.ts:1` — barrel exporting 13 sub-modules.
- `packages/frame/src/header/` — `Header`.
- `packages/frame/src/side-nav/` — `SideNav`.
- `packages/frame/src/toolbox/` — `Toolbox`, `ToolboxTabs`, `ToolboxList`.
- `packages/frame/src/toggle-toolbox/` — `ToggleToolbox`.
- `packages/frame/src/tabbed-resources/` — `TabbedResources`, `TabbedResourcesMenu`.
- `packages/frame/src/help-menu/` — `HelpMenu`.
- `packages/frame/src/theme-toggle/` — `ThemeToggle`.
- `packages/frame/src/window-controls/` — `WindowControls`.
- `packages/frame/src/sign-out/` — `SignOut`.
- `packages/frame/src/content-region/` — `ContentRegion`.
- `packages/frame/src/version-copyright/` — `VersionCopyright`.
- Reference assembly: `apps/web/sandbox-01/src/app.tsx:202` (full shell composition) and `apps/web/sandbox-tenant-manager/src/routes/__root.tsx:8`.

## Key Dependencies

- `@carbon/react` — underlying UI primitives.
- `@carbon/icons-react` — icon set.
- `@integrate-core-ui/store` — tab state (tabbed-resources, toolbox, explorer contexts).
- `@integrate-core-ui/themes` — theme context consumed by `ThemeToggle`.
- `react-resizable-panels` — split layout between content and toolbox.
- `styled-components` (via `babel-plugin-styled-components`) — component-local styling.
- `uuid` — tab id generation.

## Related Features

- [Theme Toggle](./theme-toggle.md)
- [Tabbed Resources](./tabbed-resources.md)
- [Toolbox](./toolbox.md)
- [Store Contexts (dev)](../dev/store-contexts.md)

## Change Log

- 2026-04-21: Seeded.
