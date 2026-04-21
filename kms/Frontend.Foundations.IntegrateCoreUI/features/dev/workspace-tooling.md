# Workspace & Monorepo Tooling

- **Category:** dev
- **Status:** active
- **Owners:** UI Platform Team

## Summary

pnpm-workspace monorepo (`packageManager: "pnpm@9.7.1"`) with workspace globs for `packages/*`, `apps/web/*`, and `tools/*`. Shared TypeScript path aliases, ESLint flat config, Prettier with import sorter, `.npmrc` with `auto-install-peers`, and a VS Code multi-root workspace file.

## Behavior

- `pnpm-workspace.yaml:1` declares workspace globs; excludes `packages/idm-adapter` (which uses its own `npm` + private `.npmrc`).
- `tsconfig.base.json:17` defines `@integrate-core-ui/*` path aliases → each package's `src/index.ts`: `auth`, `cookie-consent`, `form`, `formatter` (ghost), `frame`, `grid`, `modal`, `store`, `federation`, `themes`. Base settings: `moduleResolution: bundler`, `target: es2015`, `module: esnext`, `lib: [es2020, dom]`, `importHelpers`, `skipLibCheck`.
- `.npmrc:1` — `strict-peer-dependencies=false`, `auto-install-peers=true`, `save-exact=true`, `always-auth=true`, `loglevel=error`, registry `https://registry.npmjs.org/`.
- `eslint.config.cjs:1` — flat config; ignores `packages/idm-adapter`, `.verdaccio`, `storybook-static`, `dist`, `node_modules`, `coverage`, and various config files.
- `.prettierrc.cjs:1` — `@trivago/prettier-plugin-sort-imports` with custom import-order groups (third-party → `@/...` → `selectors...` → parent non-style → sibling non-style → styles → remaining relative), `printWidth: 130`, `singleQuote: true`.
- Root `package.json:5-16` — top-level scripts: `build` (formatter first, then everything else in parallel), `lint`, `typecheck`, `test`, `dev` (all web apps), `storybook`, `verdaccio`, `publish:feed`, `publish:verdaccio`.
- `.vscode/extensions.json` — recommends `esbenp.prettier-vscode`, `dbaeumer.vscode-eslint`, `ms-playwright.playwright`.
- `.vscode/settings.json` — `editor.formatOnSave: true`; hides `apps`, `packages`, `pipelines`, `tools` from Explorer (intended for use with the multi-root workspace).
- `.vscode/IntegrateCoreUiNext.code-workspace` — exposes `root`, `apps`, `packages`, `tools`, `pipelines`, `docs` as separate folders.

## Entry Points

- `pnpm-workspace.yaml:1`.
- `tsconfig.base.json:1`.
- `tsconfig.base.json:17` — path aliases.
- `.npmrc:1`.
- `eslint.config.cjs:1`.
- `.prettierrc.cjs:1`.
- Root `package.json:5-16` — workspace scripts.
- `.vscode/IntegrateCoreUiNext.code-workspace`.

## Key Dependencies

- `pnpm@9.7.1` (enforced), Node.js 22.x.
- `typescript@5.8.3`, `eslint@9.28.0`, `typescript-eslint@8.33.1`, `prettier@3.5.3`.

## Related Features

- [Husky + lint-staged](./husky-lintstaged.md)
- [Verdaccio Local Registry](./verdaccio-local-registry.md)
- [ADO Validate Pipeline](./ado-validate-pipeline.md)

## Change Log

- 2026-04-21: Seeded.
