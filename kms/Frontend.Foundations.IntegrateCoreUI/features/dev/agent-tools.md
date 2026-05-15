# Agent Tools (`@integrate-core-ui/agent-tools`)

- **Category:** dev
- **Status:** active
- **Owners:** UI Platform Team

## Summary

Published package that exposes LLM tool manifests and a React executor hook for controlling shared Integrate UI store surfaces such as tabbed resources and toolbox state.

## Behavior

- Defines 27 tool descriptions for tabbed-resource and toolbox interactions.
- Provides `useToolExecutor()` so consuming apps can dispatch validated tool calls into the shared store contexts.
- Ships as an ESM-only Vite library with generated type declarations.
- Includes package README documentation for architecture, API shape, and integration guidance.

## Entry Points

- `packages/agent-tools/src/manifest/tabbed-resources.ts` — tabbed-resource tool definitions.
- `packages/agent-tools/src/manifest/toolbox.ts` — toolbox tool definitions.
- `packages/agent-tools/src/executor/useToolExecutor.tsx` — React hook that executes tool calls against store providers.
- `packages/agent-tools/src/types.ts` — public tool-call types.
- `packages/agent-tools/README.md` — package usage and integration docs.

## Key Dependencies

- [Store Contexts](./store-contexts.md) — provides the tabbed-resource and toolbox state surfaces being controlled.

## Related Features

- [Tabbed Resources (business)](../business/tabbed-resources.md)
- [Toolbox (business)](../business/toolbox.md)
- [Store Contexts](./store-contexts.md)

## Change Log

- 2026-05-15: PR #177 feat: add @integrate-core-ui/agent-tools package for LLM tool integration — introduced the package, tool manifests, executor hook, README, and build config.
