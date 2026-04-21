# Verdaccio Local Registry

- **Category:** dev
- **Status:** active
- **Owners:** UI Platform Team

## Summary

A local npm registry (`http://localhost:4873/`) packaged under `.verdaccio/` so developers can dry-run the entire publish flow for `@integrate-core-ui/*` without hitting the ADO `rainier-latest` feed. Downstream consumers can install `@integrate-core-ui` tarballs directly from this registry.

## Behavior

- `pnpm run verdaccio` starts the registry via `.verdaccio/start.js` — it spawns `verdaccio --config config.yml`, polls the endpoint until it responds, prints a cyan URL, and auto-opens a browser.
- `pnpm run publish:verdaccio` runs the full `pnpm run build` then iterates every `packages/*` and publishes to the local registry via `.verdaccio/publish.js`.
- Storage lives at `../tmp/local-registry/storage`.
- All packages get `$all` access/publish/unpublish (per `config.yml`). Unknown packages proxy through `npmjs`.
- `publish.js` uses `--registry http://localhost:4873/ --no-git-checks --userconfig=<.verdaccio/.npmrc>`; the `--userconfig` override is needed because consuming projects often have a scoped registry pointing at `@integrate-core-ui` that would otherwise take precedence.
- Consumers can install a Verdaccio-published package via a tarball URL: `http://localhost:4873/@integrate-core-ui/<name>/-/<name>-<version>.tgz` (per `README.md:76`).

## Entry Points

- `.verdaccio/config.yml:1` — registry config; listens on `0.0.0.0:4873`; `publish.allow_offline: true`.
- `.verdaccio/.npmrc:1` — local auth with a `fake-token`.
- `.verdaccio/start.js:1` — spawns Verdaccio, polls, auto-opens browser (platform-aware: `start`/`open`/`xdg-open`).
- `.verdaccio/publish.js:1` — publishes a dist package to the local registry.
- Root `package.json:15` — `verdaccio` script → `node .verdaccio/start.js`.
- Root `package.json:11` — `publish:verdaccio` script.

## Key Dependencies

- `verdaccio@6.1.2` (devDep).

## Related Features

- [ADO Deploy Pipeline](./ado-deploy-pipeline.md) — production publish path.
- [Publish Feed Script](./publish-feed-script.md)

## Change Log

- 2026-04-21: Seeded.
