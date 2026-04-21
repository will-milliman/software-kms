# Upstream IdP Federation

- **Category:** business
- **Status:** active
- **Owners:** @milliman-lts/platform-engineering

## Summary

The IssuerService federates all primary-factor authentication to an upstream Identity Provider — **Auth0** or **Entra ID** in production, **Keycloak 26.3** in local dev — using ASP.NET Core OpenID Connect. Users log in at the upstream IdP; the Issuer then mints its own Milliman-claim-bearing tokens for downstream resource servers.

## Behavior

- `TrustedIssuer`, `Authority`, `Audience`, `MetadataAddress` are parameters in the Aspire AppHost (`AppHost.cs`), letting each environment point at a different upstream.
- In dev, Aspire launches a Keycloak container (`quay.io/keycloak/keycloak:26.3`) with the `Integrate` realm imported from `.realms/Integrate-realm.json`.
- The Issuer's OIDC-cookie pair stores the upstream session; session-expiry / logout events flow down (see `auth-server/docs/timeouts.md` four-layer model).

## Entry Points

- `auth-server/src/Integrate.SaaS.AuthServer.AppHost/AppHost.cs:1-282` — parameters + Keycloak registration.
- `auth-server/src/Integrate.SaaS.AuthServer.AppHost/HostingKeycloakExtensions.cs` — `AddHttpsKeycloak(...)` realm import.
- `auth-server/src/Integrate.SaaS.AuthServer.IssuerService/Program.cs:1-101` — OIDC + cookie auth configuration.
- `auth-server/src/Integrate.SaaS.AuthServer.Client/*` — shared OIDC events, `TicketStore`, and middleware consumed by every resource server.
- `auth-server/docs/timeouts.md` — four-layer (Entra → Auth0 → Issuer → Client) session lifetime rules.
- `auth-server/docs/AUTHENTICATION_COOKIE_CONFIGURATION.md` — cookie configuration guide.

## Key Dependencies

- `Microsoft.AspNetCore.Authentication.OpenIdConnect` 10.0.2.
- `Aspire.Hosting.Keycloak` 13.1.0-preview (dev only).
- Keycloak 26.3 container image.

## Related Features

- [OIDC Issuer](./oidc-issuer.md)
- [Keycloak Dev IdP](../dev/keycloak-dev-idp.md)
- [Data Protection & Ticket Store](../dev/data-protection-and-ticket-store.md)

## Change Log

- 2026-04-21: Seeded.
