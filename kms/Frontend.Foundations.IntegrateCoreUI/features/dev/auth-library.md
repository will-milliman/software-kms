# Auth Library (`@integrate-core-ui/auth`)

- **Category:** dev
- **Status:** active
- **Owners:** UI Platform Team

## Summary

Published library providing the `AuthManager` singleton, silent renewal, and return-URL utilities for Integrate Electron apps. Wraps `oidc-client` with typed errors, a tagged logger, and version injection.

## Behavior

- `AuthManager.getInstance(config)` returns a strict singleton wired to the supplied `SecurityConfig` (authority, client_id, scopes, timeouts, flags).
- `login()` / `logout()` / `getUser()` / `getAccessToken()` delegate to `oidc-client`, with standardized `ErrorType` failures.
- `TokenRenewalManager` runs a `setInterval` that triggers silent renewal before token expiry.
- `SilentAuth.ts` is built as a separate UMD bundle (`silentAuth.js`) to load inside the silent-renewal iframe.
- `saveReturnUrl()` / `getReturnUrl()` round-trip the pre-auth URL via browser storage.
- `AuthLogger.formatExpiry()` renders human-readable token expiries for `[Auth Manager]`-prefixed log output.
- Build-time version is injected via `__AUTH_VERSION__` define, sourced from `package.json`.

## Entry Points

- `packages/auth/src/index.ts:1` — barrel (`AuthManager`, `ErrorType`, `VERSION`, `DefaultAuthMessages`, `saveReturnUrl`, `getReturnUrl`, re-exported `User`).
- `packages/auth/src/AuthManager.ts:1` — singleton; `VERSION` (`:18`), `ErrorType` (`:30`).
- `packages/auth/src/TokenRenewalManager.ts` — silent-renewal scheduler.
- `packages/auth/src/SilentAuth.ts` — iframe callback entry.
- `packages/auth/src/utils/returnUrl.ts:1` — return-URL helpers.
- `packages/auth/src/AuthLogger.ts` — logger.
- `packages/auth/vite.config.mts` — main library build.
- `packages/auth/vite.silent-auth.config.ts` — UMD build of the silent-renewal entry.
- `packages/auth/src/__tests__/{AuthManager,TokenRenewalManager,returnUrl}.test.ts` — Vitest specs.
- `packages/auth/AGENTS.md` — documents structural sections and refactoring history.
- `packages/auth/README.md` — Quick Start, `SecurityConfig` table, flow diagram.

## Key Dependencies

- `oidc-client@1.5.4` (peer) — OIDC Implicit Flow.

## Related Features

- [Authentication (Sign-In)](../business/authentication.md)

## Change Log

- 2026-04-21: Seeded.
