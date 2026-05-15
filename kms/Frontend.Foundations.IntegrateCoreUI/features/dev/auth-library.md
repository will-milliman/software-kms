# Auth Library (`@integrate-core-ui/auth`)

- **Category:** dev
- **Status:** active
- **Owners:** UI Platform Team

## Summary

Published library providing the `AuthManager` singleton, silent renewal, typed auth response metadata, and return-URL utilities for Integrate Electron apps. Wraps `oidc-client` with internal logging and version injection.

## Behavior

- `AuthManager.getInstance(config)` returns a strict singleton wired to the supplied `SecurityConfig` (authority, client_id, scopes, timeouts, flags).
- `login()` / `logout()` / `getUser()` / `getAccessToken()` delegate to `oidc-client`; consumer error handling receives `(error, errorHint?)`.
- `AuthManager` owns silent-renewal scheduling internally, including dynamic renewal intervals based on token lifetime and capped at 15 minutes.
- Successful auth responses include computed metadata such as `maxAgeExpiry`, `reauthExpirySeconds`, `renewalIntervalSeconds`, and `tokenExpiry`.
- Silent auth updates are accepted through same-origin `auth:silent_renewal` messages.
- `loginHint` flows send `login_hint` and `prompt=select_account`; top-level `postLogoutRedirectUri` takes precedence over `securityConfig.postLogoutRedirectUri`.
- `SilentAuth.ts` is built as a separate UMD bundle (`silentAuth.js`) to load inside the silent-renewal iframe.
- `saveReturnUrl()` / `getReturnUrl()` round-trip the pre-auth URL via browser storage.
- Build-time version is injected via `__AUTH_VERSION__` define, sourced from `package.json`.

## Entry Points

- `packages/auth/src/index.ts:1` — barrel (`AuthManager`, `VERSION`, `saveReturnUrl`, `getReturnUrl`, typed auth/config exports, re-exported `User`).
- `packages/auth/src/AuthManager.ts:1` — singleton and renewal orchestration.
- `packages/auth/src/SilentAuth.ts` — iframe callback entry.
- `packages/auth/src/utils/returnUrl.ts:1` — return-URL helpers.
- `packages/auth/src/types/` — public config and auth response types.
- `packages/auth/src/utils/authLogger.ts` and `dateFormatters.ts` — internal logging helpers.
- `packages/auth/vite.config.mts` — main library build.
- `packages/auth/vite.silent-auth.config.ts` — UMD build of the silent-renewal entry.
- `packages/auth/src/__tests__/{AuthManager,SilentAuth,returnUrl}.test.ts` — Vitest specs.
- `packages/auth/README.md` — Quick Start, `SecurityConfig` table, flow diagram.

## Key Dependencies

- `oidc-client@1.5.4` (peer) — OIDC Implicit Flow.

## Related Features

- [Authentication (Sign-In)](../business/authentication.md)

## Change Log

- 2026-04-21: Seeded.
- 2026-05-15: PR #180 refactor: simplify auth manager renewal flow — consolidated renewal into `AuthManager`, added auth response metadata and same-origin silent-renewal handling, and removed `TokenRenewalManager` plus legacy error-message exports.
