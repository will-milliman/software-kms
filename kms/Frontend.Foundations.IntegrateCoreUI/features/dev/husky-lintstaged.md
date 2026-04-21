# Husky + lint-staged

- **Category:** dev
- **Status:** active
- **Owners:** UI Platform Team

## Summary

Husky installs a single `pre-commit` hook that runs `pnpm lint-staged --relative`. `lint-staged` runs ESLint + a repo-wide `pnpm -r run typecheck` on staged TS files, and Prettier on everything else.

## Behavior

- `pre-commit`:
  - `{apps,packages}/**/*.{ts,tsx}` → `eslint --no-error-on-unmatched-pattern <files>` then `pnpm -r run typecheck` (repo-wide; potentially slow but enforces cross-package type consistency).
  - `{apps,packages}/**/*.{ts,tsx,js,jsx,json,css,scss,md}` → `prettier --write`.
- ESLint flat config (`eslint.config.cjs`) combines `@eslint/js` recommended + `typescript-eslint` + `eslint-plugin-react`, with `consistent-type-imports: error`, `jsx-boolean-value: never`, `jsx-sort-props` (`ref`, `key` first).
- Prettier config enforces sorted imports (`@trivago/prettier-plugin-sort-imports`), `printWidth: 130`, `singleQuote: true`, `arrowParens: 'avoid'`.
- No `commit-msg` hook.

## Entry Points

- `.husky/pre-commit:1` — `pnpm lint-staged --relative`.
- `lint-staged.config.cjs:1-7` — globs and commands.
- `eslint.config.cjs:1-40` — flat config (ignores `packages/idm-adapter`, `.verdaccio`, lockfiles, build outputs).
- `.prettierrc.cjs:1-22` — formatter config + import order groups.
- Root `package.json:9` — `prepare: "husky"` installs hooks on `pnpm install`.

## Key Dependencies

- `husky@9.1.7`, `lint-staged@16.1.0`, `eslint@9.28.0`, `prettier@3.5.3`, `@trivago/prettier-plugin-sort-imports`.

## Related Features

- [Workspace & Monorepo Tooling](./workspace-tooling.md)

## Change Log

- 2026-04-21: Seeded.
