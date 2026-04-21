# Dev Flags

- **Category:** dev
- **Status:** active
- **Owners:** desktop-ui / devex

## Summary

`dev-flags.json` is a developer-only mechanism for Integrate that selectively replaces bundled local services (Project Manager, Run Manager, Governance, etc.) with locally-running dev instances, enabling edit-refresh workflows against the real Electron app.

## Behavior

- When present at `%appdata%/INTEGRATE/dev-flags.json`, Integrate reads it during startup.
- Each entry points a service key at a local URL, overriding the embedded service launcher for that component.
- Mixing is fully supported — any subset of services may run from source while the rest remain bundled.
- Not intended for production; gated behind documented dev workflow.

## Entry Points

- Root `README.md` "Dev Flags" section.
- Electron main-process loader: `monorepo/desktop/ui/apps/electron/src/` service-launcher modules.

## Key Dependencies

- [Feature Toggles (infrastructure)](feature-toggles-infrastructure.md).

## Related Features

- [SignalR Hubs](signalr-hubs.md)
- [Health Checks & /version endpoints](health-checks-and-version.md)

## Change Log

- 2026-04-21: Seeded.
