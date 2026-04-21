# Authentication (Sign-In)

- **Category:** business
- **Status:** active
- **Owners:** UI Platform Team

## Summary

OIDC-based sign-in flow for Integrate Electron desktop applications. Users authenticate against a configured identity provider; tokens are isolated in a hidden Electron renderer, silently renewed before expiry, and the pre-auth URL is restored after redirect.

## Behavior

- On startup (or unauthorized API response), the app invokes the OIDC Implicit Flow via `@integrate-core-ui/auth`'s `AuthManager`.
- Authentication runs in a **hidden Electron renderer window** — the access/id tokens live only in that window's `localStorage`, isolated from the main UI renderer (architectural invariant from ADR 2025-03-01).
- A `TokenRenewalManager` schedules silent renewal (hidden iframe) before the token expires, keeping the session alive without user interaction.
- If the user was navigating to a deep URL when the auth redirect happened, `saveReturnUrl()` persists it; after successful auth, `getReturnUrl()` restores the destination.
- Failures surface via typed `ErrorType` categories (`AuthManager.ts:30`) so the UI can show actionable messages from `DefaultAuthMessages`.
- The module was rolled out behind a **feature flag** alongside the legacy AngularJS auth (ADR 2025-03-01).

## Entry Points

- `packages/auth/src/AuthManager.ts:1` — `AuthManager` singleton (≈536 lines); `VERSION` at `:18`, `ErrorType` at `:30`.
- `packages/auth/src/TokenRenewalManager.ts` — silent-renewal scheduler.
- `packages/auth/src/SilentAuth.ts` — iframe callback entry, built as UMD `silentAuth.js` via `packages/auth/vite.silent-auth.config.ts`.
- `packages/auth/src/utils/returnUrl.ts:1` — `saveReturnUrl()`, `getReturnUrl()`.
- `packages/auth/src/AuthLogger.ts` — `[Auth Manager]` prefixed logger.
- `packages/auth/README.md` — extensive Quick Start, `SecurityConfig` table, flow diagram.

## Key Dependencies

- `oidc-client@1.5.4` (peer) — OIDC Implicit Flow primitives.
- Electron — multi-renderer window isolation for token safekeeping.

## Related Features

- [Auth Library (dev)](../dev/auth-library.md)
- [App Shell Frame](./app-shell-frame.md) — hosts the `SignOut` component.

## Change Log

- 2026-04-21: Seeded.
