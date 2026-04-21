# SignalR Hubs

- **Category:** dev
- **Status:** active
- **Owners:** backend-platform

## Summary

Each local .NET service exposes a SignalR hub for real-time status, progress, and event streaming (projection job progress, ACL changes, governance events). The desktop UI and other services subscribe to these hubs.

## Behavior

- Hubs inherit from standard ASP.NET Core SignalR `Hub` base.
- Authentication uses the same JWT bearer flow as REST endpoints — see [JWT Auth (Online + Offline)](jwt-auth.md).
- Messages are strongly typed; client proxies are generated or hand-rolled in TypeScript for the renderer.
- ACL service also exposes an event hub so other services can subscribe to permission changes.

## Entry Points

- Server-side: hub classes under `src/server/<Service>/Hubs/`.
- Client-side: `monorepo/desktop/ui/packages/*/src/signalr/`.

## Key Dependencies

- [JWT Auth (Online + Offline)](jwt-auth.md).

## Related Features

- [Run Projections](../business/run-projections.md)
- [ACL / Permissions](../business/acl-permissions.md)

## Change Log

- 2026-04-21: Seeded.
