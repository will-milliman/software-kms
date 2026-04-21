# Startup Upgrader (schema migrations)

- **Category:** dev
- **Status:** active
- **Owners:** backend-platform

## Summary

Each local .NET service runs a `StartupUpgrader` at launch that inspects its persistent store (SQLite/LocalDB/files) and applies an ordered list of migrations before the service starts serving requests.

## Behavior

- Migrations are code-based classes registered in a list.
- Upgrader records the highest applied version so migrations run exactly once.
- Failures abort startup and surface to the Electron main process, which shows a clear error to the user.
- Also used to repair or re-derive caches on upgrade.

## Entry Points

- `src/server/<Service>/Startup/StartupUpgrader.cs` (per service).
- Migration classes alongside.

## Key Dependencies

- Per-service persistence layer.

## Related Features

- [MediatR CQRS Pipeline](mediatr-cqrs.md)
- [Health Checks & /version endpoints](health-checks-and-version.md)

## Change Log

- 2026-04-21: Seeded.
