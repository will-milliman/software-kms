# Notes

Append-only change log maintained by `/pull`.

- 2026-04-23: PR #167 Apply Milliman theme refinements and stabilize toolbox layout (will-milliman, AB#91372) — Milliman light/dark (g100) token refresh; removed `variables.scss`; `Toolbox`/`ToolboxTabs`/`TabbedResources` reworked to flex layout with fixed viewport height; non-visible toolbox tabs stay mounted; `HelpMenu` simplified to Carbon `OverflowMenu`; `VersionCopyright` uses Carbon label tokens; ag-grid uses `--cds-text-primary` / `--cds-layer`; `updateActiveByIndex` updates synchronously by id with index guards; bumps `@integrate-core-ui/frame` 7.1.0, `grid` 2.0.1, `themes` 4.0.0.
- 2026-04-23: PR #166 fix: publish idm-adapter via npm@1 task with feed auth (greg-duncan, AB#91070) — replaced the 3-step copy/auth/publish sequence in `pipelines/deploy.yml` with a single `npm@1` task scoped via `workingDir: packages/idm-adapter`, matching the working pattern used for other workspace packages.
