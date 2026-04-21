# OIDC Issuer

- **Category:** business
- **Status:** active
- **Owners:** @milliman-lts/platform-engineering

## Summary

A self-hosted, hand-rolled OIDC / OAuth2 authorization server that mints access, ID, and refresh tokens bearing Milliman-specific tenant / environment / ring / permission / group / tier / location / customer claims for consumer apps across the Integrate SaaS platform.

## Behavior

- Exposes standard OIDC endpoints: `/authorize`, `/token`, `/.well-known/openid-configuration`, JWKS, and `/logout`.
- Supports authorization-code flow (PKCE) as the primary grant; hybrid/implicit code paths exist per `ProtocolResponseFactory` split tests.
- Persists authorization codes in `auth-codes` blob and refresh tokens in `auth-refresh` blob; expired records pruned by background services.
- Federates upstream to Auth0 / Entra ID (prod) or Keycloak (dev) via `OpenIdConnect`.
- Issues Milliman custom claims under `http://schemas.milliman.com/identity/claims/*` (see `domain.md`).
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
