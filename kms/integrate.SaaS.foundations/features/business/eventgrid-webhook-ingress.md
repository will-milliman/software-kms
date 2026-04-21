# EventGrid Webhook Ingress

- **Category:** business
- **Status:** active
- **Owners:** @milliman-lts/platform-engineering

## Summary

`Integrate.SaaS.AppServices.WebHookRouterService` is a minimal-API endpoint `POST /{tenant}/api/events` that receives Azure EventGrid push events, performs the EventGrid subscription-validation handshake, and dispatches validated events to downstream handlers. Protected by dual-scheme Entra ID JwtBearer.

## Behavior

- Route is gated at the ingress by headers `aeg-subscription-name` and `aeg-event-type` (ensures only EventGrid traffic hits this cluster).
- On subscription validation (`aeg-event-type: SubscriptionValidation`) the handler completes the handshake synchronously.
- Supports dual Entra ID authentication schemes for multi-tenant / multi-subscription EventGrid topics.
- All requests carry an audit-log event via the `Integrate.SaaS.AuditLog.Client` SDK.

## Entry Points

- `auth-server/src/Integrate.SaaS.AppServices.WebHookRouterService/Program.cs:1-36` — host + endpoint registration.
- `auth-server/src/Integrate.SaaS.AppServices.WebHookRouterService/WebApplicationBuilderExtensions.cs:13-52` — dual-scheme JwtBearer + handshake handler.
- `auth-server/src/Integrate.SaaS.AppServices.WebHookRouterService/EventGridRequestHandler.cs` — event dispatch.
- Ingress route `webhook` — `auth-server/src/Integrate.SaaS.AuthServer.Ingress/appsettings.json`.

## Key Dependencies

- `Microsoft.AspNetCore.Authentication.JwtBearer` (dual-scheme).
- `Integrate.SaaS.AuditLog.Client`.

## Related Features

- [YARP Ingress Gateway](../dev/yarp-ingress-gateway.md)
- [Audit Logging](./audit-logging.md)

## Change Log

- 2026-04-21: Seeded.
