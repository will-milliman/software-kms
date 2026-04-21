# Storybook Host

- **Category:** dev
- **Status:** active
- **Owners:** UI Platform Team

## Summary

A single Storybook instance under `tools/storybook-host/` that aggregates `*.stories.*` files from every workspace package. Built via `@storybook/react-vite` and deployed to Azure Blob Storage as part of the deploy pipeline.

## Behavior

- Dev: `pnpm storybook` (root) → `pnpm --filter storybook-host run storybook:dev` on port 6006.
- Build: `pnpm --filter storybook-host run storybook:build` outputs to `dist/tools/storybook-host`.
- Deploy: `pipelines/deploy.yml:134` uploads the build output to `ltssharedassets/$web/IntegrateCoreUiNext/storybook` via `AzureFileCopy@3`.
- Addons are disabled — `main.ts` provides an empty `addons: []` and turns off the `interactions` and `actions` features explicitly.
- `viteFinal` hook augments the Vite config with `@vitejs/plugin-react` + `vite-tsconfig-paths` and SCSS `api: 'modern-compiler'`.
- `tsconfig.storybook.json` includes `*.stories.*` and `.storybook/*` files and enables `emitDecoratorMetadata`.

## Entry Points

- `tools/storybook-host/.storybook/main.ts:8` — stories glob `../../../packages/**/*.stories.@(js|jsx|ts|tsx|mdx)`.
- `tools/storybook-host/.storybook/main.ts:9` — empty addons array.
- `tools/storybook-host/.storybook/main.ts:11-14` — disabled features.
- `tools/storybook-host/.storybook/main.ts:16-27` — `viteFinal` hook.
- `tools/storybook-host/.storybook/preview.ts:1` — only `parameters.controls.expanded = true`.
- `tools/storybook-host/.storybook/manager-head.html` — favicon injection.
- `tools/storybook-host/package.json` — `storybook:dev`, `storybook:build` scripts.
- `tools/storybook-host/tsconfig.storybook.json` — Storybook-specific tsconfig.

## Key Dependencies

- `storybook@8.6.12`, `@storybook/react@8.6.12`, `@storybook/react-vite@8.6.12`.
- `vite@6.3.5`, `@vitejs/plugin-react`, `vite-tsconfig-paths`, `sass`.

## Related Features

- [ADO Deploy Pipeline](./ado-deploy-pipeline.md)
- [Data Grid](./data-grid.md), [Progress Button](./progress-button.md), [App Shell Frame (business)](../business/app-shell-frame.md) — packages with stories.

## Change Log

- 2026-04-21: Seeded.
