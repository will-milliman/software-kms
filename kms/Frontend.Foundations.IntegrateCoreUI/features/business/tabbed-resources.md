# Tabbed Resources

- **Category:** business
- **Status:** active
- **Owners:** UI Platform Team

## Summary

A tab bar at the top of the content region where users open, switch between, pin, and close "resources" (documents, projects, grids, etc.). Resources are registered via side-nav or other app actions and surface as Carbon-styled tabs.

## Behavior

- Clicking a SideNav item (or other trigger) calls `addResource(...)` and opens a new tab.
- The newly added tab becomes active; clicking other tabs activates them. An active-tab `ResourceContext` is exposed via `useResource()` for the tab's content subtree.
- Tabs can be pinned (persisted visually on the left) or closed.
- A `TabbedResourcesMenu` provides overflow access when tabs exceed visible width.
- Resource visibility, active state, and container index are driven by the `TabItem` record managed by `createTabContext`.

## Entry Points

- `packages/frame/src/tabbed-resources/` — `TabbedResources`, `TabbedResourcesMenu`.
- `packages/store/src/contexts/tabbed-resources.context.tsx` — `TabbedResourcesProvider`, `useTabbedResources`, `ResourceContext`, `useResource`.
- `packages/store/src/contexts/createTabContext.tsx:49` — underlying factory.
- Reference: `apps/web/sandbox-01/src/app.tsx:245` (tabs render) and `:266-322` (SideNav → `addResource(...)` wiring).

## Key Dependencies

- `@carbon/react` — Tab primitives.
- `@integrate-core-ui/store` — state provider and hooks.
- `uuid` — tab id generation.

## Related Features

- [App Shell Frame](./app-shell-frame.md)
- [Toolbox](./toolbox.md)
- [Store Contexts (dev)](../dev/store-contexts.md)

## Change Log

- 2026-04-21: Seeded.
