# OIDC Issuer

- **Category:** business
- **Status:** active
- **Owners:** @milliman-lts/platform-engineering

## Summary

A self-hosted, hand-rolled OIDC / OAuth2 authorization server that mints access, ID, and refresh tokens bearing Milliman-specific tenant / environment / ring / permission / group / tier / location / customer claims for consumer apps across the Integrate SaaS platform.

## Behavior

- Exposes standard OIDC endpoints: `/authorize`, `/token`, `/.well-known/openid-configuration`, JWKS, and `/logout`.
- Supports authorization-code flow (PKCE) as the primary grant; hybrid/implicit code paths exist per `ProtocolResponseFactory` split tests.
- Persists authorization codes in `auth-codes` blob and refresh tokens in `auth-refresh` blob; storage-account lifecycle policy owns expiry cleanup rather than issuer background workers.
- Federates upstream to Auth0 / Entra ID (prod) or Keycloak (dev) via `OpenIdConnect`.
- Issues Milliman custom claims under `http://schemas.milliman.com/identity/claims/*` (see `domain.md`).
- Authorization responses expose both access-token expiry (`expires_in`) and remaining issuer session lifetime (`session_expires_in`) so clients can distinguish API token lifetime from interactive session lifetime.
- Applies OWASP security headers and cert-based data protection to session state.

## Entry Points

- `auth-server/src/Integrate.SaaS.AuthServer.IssuerService/Program.cs:1-101` — host bootstrap; `AddOpenIdIssuer`, upstream cookie+OIDC auth, antiforgery, audit sidecar, well-known endpoints, `MapLogout`, MVC.
- `auth-server/src/Integrate.SaaS.AuthServer/DependencyInjection/OpenIdIssuerExtensions.cs:1-90` — issuer registration (stores, services, cleanup workers).
- `auth-server/src/Integrate.SaaS.AuthServer/Services/AuthorizationService.cs` — authorize flow validation + code issuance.
- `auth-server/src/Integrate.SaaS.AuthServer/Services/ProtocolResponseFactory.cs` — mints Code/Hybrid/Implicit responses and access/id/refresh tokens.
- `auth-server/src/Integrate.SaaS.AuthServer/WellKnownEndpoints/WellKnownMiddleware.cs` — `/.well-known/openid-configuration` + JWKS.
- `auth-server/src/Integrate.SaaS.AuthServer/Logout/LogoutEndpointRouteBuilderExtensions.cs` — `MapLogout()`.
- `auth-server/src/Integrate.SaaS.AuthServer/Services/TokenValidator.cs`, `RequestValidationService.cs` — protocol validation.

## Key Dependencies

- `Microsoft.AspNetCore.Authentication.OpenIdConnect` 10.0.2 — upstream OIDC federation.
- `Microsoft.AspNetCore.DataProtection.AzureStorage` — key ring persistence.
- `OwaspHeaders.Core` 10.2.0 — security headers.
- Azure Blob Storage (`auth-codes`, `auth-refresh`, `auth-dpapi`, `auth-sessions`, `auth-users`, `auth-clients`, `auth-tenants`).
- Internal `Integrate.SaaS.AuthServer` core library.

## Related Features

- [Upstream IdP Federation](./upstream-idp-federation.md)
- [Admin Portal](./admin-portal.md)
- [Ring-Based Rollout](./ring-based-rollout.md)
- [YARP Ingress Gateway](../dev/yarp-ingress-gateway.md)

## Change Log

- 2026-04-21: Seeded.
- 2026-05-07: PR #392 AB#91472 fix(issuer): remove redundant RefreshTokenCleanupService registration — removed the refresh-token cleanup hosted-service registration in favor of storage lifecycle management.
- 2026-05-07: PR #393 AB#91484 fix(issuer): remove RefreshTokenCleanupService — removed refresh-token and authorization-code cleanup services/options so lifecycle policies handle expired blobs.
- 2026-05-07: PR #394 AB#91508 Enhance error logging and handling for authorization codes — token redemption now treats authorization codes as strictly single-use, fails if deletion fails, and logs/traces the effective client ID.
- 2026-05-07: PR #414 AB#91708 Enhance authentication flow with diagnostics, logging, and documentation — added antiforgery failure redirects, auth redirect-loop detection, ticket-store error logging, and auth-server developer docs.
- 2026-05-07: PR #437 AB#91963 Enhance session management with max age and renewal handling — silent renewal is denied with `login_required` when a session is too close to max age, and protocol errors map to 401 responses.
- 2026-05-07: PR #438 AB#91971 Include max age limit in session authorization response — implicit authorization responses now include and propagate the OIDC `max_age` value.
- 2026-05-07: PR #439 AB#91971 Fix MaxAge handling in authorization requests and responses — response building now uses request `MaxAge` when provided and otherwise falls back to the configured default.
- 2026-05-11: PR #448 AB#91423 Allow anonymous access to health check endpoints — health-check endpoints now opt out of issuer authentication requirements.
- 2026-05-11: PR #449 Bypass claims middleware for anonymous endpoints — anonymous endpoints bypass tenant/environment/ring claims middleware before authorization is evaluated.
- 2026-05-12: PR #458 AB#92136: Fix IdToken expiration handling and update authorization response — ID token expiry is calculated from authentication time, access token expiry from issue time, and implicit redirects include `session_expires_in`.
- 2026-05-13: PR #469 AB#92136: Refactor session renewal logic to enforce max age limit — silent renewal now denies only sessions that meet or exceed `MaxAge`, removing the separate minimum-remaining-session-time option.
