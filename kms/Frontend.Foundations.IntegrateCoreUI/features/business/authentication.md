# Authentication (Sign-In)

- **Category:** business
- **Status:** active
- **Owners:** UI Platform Team

## Summary

OIDC-based sign-in flow for Integrate Electron desktop applications. Users authenticate against a configured identity provider; tokens are isolated in a hidden Electron renderer, silently renewed by `AuthManager`, and the pre-auth URL is restored after redirect.

## Behavior

- On startup (or unauthorized API response), the app invokes the OIDC Implicit Flow via `@integrate-core-ui/auth`'s `AuthManager`.
- Authentication runs in a **hidden Electron renderer window** — the access/id tokens live only in that window's `localStorage`, isolated from the main UI renderer (architectural invariant from ADR 2025-03-01).
- `AuthManager` schedules silent renewal before token expiry, supports same-origin silent-renewal messages, and calculates renewal timing from token lifetime.
- Auth response metadata exposes max-age and token-expiry timing so consuming applications can drive reauthentication UX.
- If the user was navigating to a deep URL when the auth redirect happened, `saveReturnUrl()` persists it; after successful auth, `getReturnUrl()` restores the destination.
- Failures surface as an `Error` plus optional user-facing `errorHint`; legacy configurable auth message overrides were removed.
- The module was rolled out behind a **feature flag** alongside the legacy AngularJS auth (ADR 2025-03-01).

## Entry Points

- `packages/auth/src/AuthManager.ts:1` — `AuthManager` singleton (≈536 lines); `VERSION` at `:18`, `ErrorType` at `:30`.
- `packages/auth/src/SilentAuth.ts` — iframe callback entry, built as UMD `silentAuth.js` via `packages/auth/vite.silent-auth.config.ts`.
- `packages/auth/src/utils/returnUrl.ts:1` — `saveReturnUrl()`, `getReturnUrl()`.
- `packages/auth/src/types/AuthResponses.ts` — typed auth response metadata.
- `packages/auth/README.md` — extensive Quick Start, `SecurityConfig` table, flow diagram.

## Key Dependencies

- `oidc-client@1.5.4` (peer) — OIDC Implicit Flow primitives.
- Electron — multi-renderer window isolation for token safekeeping.

## Related Features

- [Auth Library (dev)](../dev/auth-library.md)
- [App Shell Frame](./app-shell-frame.md) — hosts the `SignOut` component.

## Change Log

- 2026-04-21: Seeded.
- 2026-05-15: PR #180 refactor: simplify auth manager renewal flow — auth renewal is now internal to `AuthManager`, responses expose expiry metadata, and consumer error callbacks were simplified.
