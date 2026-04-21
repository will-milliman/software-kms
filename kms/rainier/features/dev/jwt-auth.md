# JWT Auth (Online + Offline)

- **Category:** dev
- **Status:** active
- **Owners:** backend-platform / identity

## Summary

`Rainier.ServiceToolkit` provides JWT bearer authentication for all rainier services. Online services validate against the Integrate gateway/OIDC provider; desktop-local services use an "offline" JWT profile with long lifetimes and a local signing key so the desktop UI can call them without an internet round-trip.

## Behavior

- Each service registers the toolkit's auth middleware at startup.
- Offline mode accepts tokens signed by a key shared between the Electron main process and the local services (provisioned at launch).
- Online mode validates standard OIDC tokens from the gateway.
- Tokens carry claims consumed by [RequiresPermission Authorization](requires-permission.md).

## Entry Points

- `src/shared/Rainier.ServiceToolkit/Auth/` (approximate).
- Service `Startup`/`Program.cs` `AddAuthentication()` calls.

## Key Dependencies

- [Integrate Auth & Gateways](../business/integrate-auth-and-gateways.md) for the online path.

## Related Features

- [SignalR Hubs](signalr-hubs.md)
- [RequiresPermission Authorization](requires-permission.md)

## Change Log

- 2026-04-21: Seeded.
