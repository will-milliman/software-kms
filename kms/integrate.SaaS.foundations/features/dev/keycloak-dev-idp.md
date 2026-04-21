# Keycloak Dev IdP

- **Category:** dev
- **Status:** active
- **Owners:** @milliman-lts/platform-engineering

## Summary

In production the auth-server federates upstream to **Auth0** or **Entra ID**. For local development, Aspire launches a **Keycloak 26.3** container with the `Integrate` realm imported from a JSON file, so developers can exercise the full OIDC federation flow without touching cloud IdPs.

## Behavior

- Keycloak container: `quay.io/keycloak/keycloak:26.3`.
- Realm imported on startup from `.realms/Integrate-realm.json` — contains test clients, users, groups, and roles.
- Admin credentials provided as Aspire parameters.
- Reachable via HTTPS (the `AddHttpsKeycloak` extension handles TLS).

## Entry Points

- `auth-server/src/Integrate.SaaS.AuthServer.AppHost/AppHost.cs:1-282` — Keycloak resource registration.
- `auth-server/src/Integrate.SaaS.AuthServer.AppHost/HostingKeycloakExtensions.cs` — `AddHttpsKeycloak(...)` including realm import.
- `auth-server/src/Integrate.SaaS.AuthServer.AppHost/.realms/Integrate-realm.json` — seed realm.

## Key Dependencies

- `Aspire.Hosting.Keycloak` 13.1.0-preview.
- `quay.io/keycloak/keycloak:26.3` image.

## Related Features

- [Upstream IdP Federation](../business/upstream-idp-federation.md)
- [Aspire AppHost Orchestration](./aspire-apphost-orchestration.md)

## Change Log

- 2026-04-21: Seeded.
