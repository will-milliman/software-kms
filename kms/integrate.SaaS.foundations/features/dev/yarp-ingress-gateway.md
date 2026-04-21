# YARP Ingress Gateway

- **Category:** dev
- **Status:** active
- **Owners:** @milliman-lts/platform-engineering

## Summary

The auth-server ships a **YARP reverse proxy** (`Integrate.SaaS.AuthServer.Ingress`) that serves as the single HTTP entry point for all gateway services and uses Aspire service discovery to resolve destinations. Tenant-admin includes a **local-dev simulator** (`Integrate.Gateway.Ingress`) that provides host-based routing between Api + Web on port 44300.

## Behavior

### auth-server Ingress

Routes declared in `appsettings.json:10-128`:

| Route | Path | Cluster |
|---|---|---|
| `issuer` | `/**` catch-all | `https://issuer` |
| `config` | `configuration/**` | `https://configuration-service` |
| `users`, `groups` | `users/**`, `groups/**` | `https://user-service` |
| `portal`, `portal-auth` | `portal/**`, `signin-portal/**` | `https://portal-service` |
| `downloads`, `release-notes` | `api/downloads/**`, `api/releasenotes/**` | `https://update-service` |
| `webhook` | `**` + `aeg-subscription-name`/`aeg-event-type` headers | `https://webhook-router` |

Destinations resolved via `AddServiceDiscoveryDestinationResolver` (Aspire).

### tenant-admin Ingress

Local-dev host-routing simulator for exercising Api + Web behind a shared host on 44300.

## Entry Points

- `auth-server/src/Integrate.SaaS.AuthServer.Ingress/Program.cs:1-13` — YARP bootstrap + service discovery.
- `auth-server/src/Integrate.SaaS.AuthServer.Ingress/appsettings.json:10-128` — routes + clusters.
- `tenant-admin/src/Integrate.Gateway.Ingress/Program.cs:1-13` — local-dev ingress.

## Key Dependencies

- `Yarp.ReverseProxy` 2.3.0.
- `Microsoft.Extensions.ServiceDiscovery.Yarp` 10.2.0 / 9.4.2.

## Related Features

- [Aspire AppHost Orchestration](./aspire-apphost-orchestration.md)
- [OIDC Issuer](../business/oidc-issuer.md)

## Change Log

- 2026-04-21: Seeded.
