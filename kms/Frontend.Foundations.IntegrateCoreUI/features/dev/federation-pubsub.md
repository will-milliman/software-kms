# Federation Pub/Sub (`@integrate-core-ui/federation`)

- **Category:** dev
- **Status:** active (types + runtime present; no active MF host/remote yet)
- **Owners:** UI Platform Team

## Summary

Shared contract and runtime for host/remote communication in a future Module Federation world. Provides an emitter factory (backed by `tseep`) and a React context so MFE remotes can consume host-published state and events without direct props.

## Behavior

- `createEmitter({ tracePrefix })` returns a frozen `Emitter` with `on/off/emit` methods. `tracePrefix` (default `[One Integrate]`) is prepended to a `console.info` trace on each emit, aiding debugging across MFE boundaries.
- `<IcuEmitterProvider value={emitter}>` installs the emitter into React context.
- `useEmitter()` returns the emitter; **throws** if no provider is present, forcing callers to opt in explicitly.
- `MfeProps` documents the minimal contract a Remote component is expected to receive from the Host.
- `CreateEmitterOptions` and `IcuEmitterProviderProps` are exported for host/remote integrators.

## Entry Points

- `packages/federation/src/index.ts:1-10` — types + runtime exports.
- `packages/federation/src/create-emitter.ts:62` — `createEmitter` factory.
- `packages/federation/src/emitter-context.tsx:18` — `IcuEmitterProvider`.
- `packages/federation/src/emitter-context.tsx:27` — `useEmitter` (throws if missing).
- `packages/federation/README.md` — usage for Remotes and standalone dev mode.

## Key Dependencies

- `tseep@1.3.1` — minimal, typed event emitter.
- React 19 (peer).

## Related Features

- [Planned MFE Architecture](./mfe-architecture-planned.md)

## Change Log

- 2026-04-21: Seeded.
