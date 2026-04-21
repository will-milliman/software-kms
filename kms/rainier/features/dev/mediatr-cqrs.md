# MediatR CQRS Pipeline

- **Category:** dev
- **Status:** active
- **Owners:** backend-platform

## Summary

Rainier.Server.Core wraps MediatR with a `CommandDispatcher` and a `CommandPipelineBehavior` that add validation, logging, authorization ([RequiresPermission Authorization](requires-permission.md)), and transaction boundaries around every command/query.

## Behavior

- Controllers/SignalR hubs call `ICommandDispatcher.Send(command)` rather than invoking handlers directly.
- `CommandPipelineBehavior<TRequest, TResponse>` intercepts every request: permission check → validation → logging → handler → post-processing.
- Queries use a parallel `IQueryDispatcher` with a lighter behavior chain.

## Entry Points

- `src/server/Rainier.Server.Core/Mediation/CommandDispatcher.cs`.
- `src/server/Rainier.Server.Core/Mediation/CommandPipelineBehavior.cs`.

## Key Dependencies

- MediatR (NuGet).
- [RequiresPermission Authorization](requires-permission.md).

## Related Features

- [Hypermedia (HAL) Responses](hypermedia-hal.md)
- [Startup Upgrader (schema migrations)](startup-upgrader.md)

## Change Log

- 2026-04-21: Seeded.
