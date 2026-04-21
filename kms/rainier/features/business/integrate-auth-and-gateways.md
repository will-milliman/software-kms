# Integrate Auth & Gateways

- **Category:** business
- **Status:** active
- **Owners:** frontend-platform-engineering

## Summary

At app startup, users select a **gateway** (Dev/QA/Prod/customer-specific cloud endpoint) and then sign in via OIDC. The Electron shell orchestrates this through a state machine with dedicated windows.

## Behavior

- First launch: Gateway Selection window.
- User picks/adds a gateway; Electron persists the choice.
- Auth window opens (OIDC via `@integrate-core-ui/auth`); on success the main app window opens with the user's JWT.
- Sign-out returns to the gateway/auth state.
- The JWT is the basis for all service calls (cloud and local).

## Entry Points

- `monorepo/desktop/ui/apps/electron/src/main/integrateApp.ts:57-110` — state transitions (`INITIAL → GATEWAY_SELECTION → AUTH → MAIN → SHUTTING_DOWN`).
- `.../state-machine/stateMachine.ts`.
- `.../renderers/gateway/gatewayWindow.ts`.
- `.../renderers/auth/authWindow.ts`.
- `.../renderers/app/appWindow.ts:25`.
- IPC events: `.../main/ipc-events/authEvents.ts`, `gatewayEvents.ts`.
- Service: `.../services/auth.ts`, `userStore.ts`.

## Key Dependencies

- `@integrate-core-ui/auth` (external).
- Certificate handler for Windows cert interactions.
- Unauthorized handler for 401 flows.

## Related Features

- [JWT Auth (Online + Offline)](../dev/jwt-auth.md) — dev counterpart.

## Change Log

- 2026-04-21: Seeded.
