# Health Checks & /version endpoints

- **Category:** dev
- **Status:** active
- **Owners:** backend-platform

## Summary

Every rainier service exposes standard well-known endpoints: `/health` (and/or `/healthz`) for liveness/readiness probes and `/version` for runtime version info. Used by the Electron launcher to detect readiness and by Azure monitoring for cloud services.

## Behavior

- Health checks aggregate dependencies (DB, downstream services) through `IHealthCheck` implementations.
- `/version` returns semver, build number, commit SHA, and build date.
- Electron main-process polls `/health` after starting a local service before dispatching UI requests.

## Entry Points

- Per service: `Startup`/`Program.cs` `AddHealthChecks()` + a version controller/endpoint.

## Key Dependencies

- ASP.NET Core `Microsoft.Extensions.Diagnostics.HealthChecks`.

## Related Features

- [Startup Upgrader (schema migrations)](startup-upgrader.md)
- [Dev Flags](dev-flags.md)

## Change Log

- 2026-04-21: Seeded.
