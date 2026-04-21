# Publish Feed Script

- **Category:** dev
- **Status:** active
- **Owners:** UI Platform Team

## Summary

`tools/publish-feed.ts` is an idempotent wrapper around `npm publish` used by the deploy pipeline to push pre-built package directories to the ADO `rainier-latest` feed. Treats "version already exists" errors as success so re-runs of the pipeline don't fail.

## Behavior

- Takes a dist package path as the first argv.
- Runs `npm publish <distPath> --no-git-checks`.
- If the command fails with an "already exists" pattern on stderr (regex matches: `the feed already contains file`, `cannot publish over existing version`, `EPUBLISHCONFLICT`, `E403 ... already exists`), exits **0** with a warning printed. Otherwise exits with npm's non-zero status.
- This enables re-triggering the deploy pipeline without version bumps for unrelated fixes, while still surfacing real publish errors.

## Entry Points

- `tools/publish-feed.ts:10` — `npm publish ... --no-git-checks`.
- `tools/publish-feed.ts:34-48` — idempotency regex + exit 0 logic.
- Used in `pipelines/deploy.yml:86-87` for `idm-adapter`.
- Referenced indirectly by `pnpm run publish:feed` (`package.json:10`) which iterates `packages/*` and calls each package's own `publish:feed` script.

## Key Dependencies

- Node's `child_process` / `tsx` runtime for direct TS execution.
- `npm` CLI.

## Related Features

- [ADO Deploy Pipeline](./ado-deploy-pipeline.md)
- [Verdaccio Local Registry](./verdaccio-local-registry.md)

## Change Log

- 2026-04-21: Seeded.
