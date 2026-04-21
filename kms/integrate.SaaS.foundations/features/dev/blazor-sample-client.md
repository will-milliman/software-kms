# Blazor Sample Client

- **Category:** dev
- **Status:** active
- **Owners:** @milliman-lts/platform-engineering

## Summary

`Integrate.SaaS.AuthServer.SampleApp` is a **Blazor Server** reference OIDC client bundled with the auth-server solution. It serves as a live integration example, as a smoke-test harness during dev, and as a template for downstream teams building Blazor apps that consume the Issuer.

## Behavior

- OIDC code-flow login/logout endpoints.
- Typed HttpClient to `https+http://gateway` (via Aspire service discovery) — so it talks to the Ingress and through it to the Issuer and resource services.
- Cert-based DPAPI keyring persisted to the `sample-dpapi` blob container; cookie ticket store in `sample-sessions`.
- Default Blazor scaffolding (`Components/App.razor`, `Routes`, `Layout`, `Pages/Home`, `Counter`, `Error`) plus a `LoginDisplay.razor`.

## Entry Points

- `auth-server/src/Integrate.SaaS.AuthServer.SampleApp/Program.cs` — host + auth endpoints.
- `auth-server/src/Integrate.SaaS.AuthServer.SampleApp/Components/` — Blazor UI.

## Key Dependencies

- `Microsoft.AspNetCore.Authentication.OpenIdConnect`.
- `Integrate.SaaS.AuthServer.Client`.

## Related Features

- [OIDC Issuer](../business/oidc-issuer.md)
- [YARP Ingress Gateway](./yarp-ingress-gateway.md)

## Change Log

- 2026-04-21: Seeded.
