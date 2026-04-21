# ADO Deploy Pipeline

- **Category:** dev
- **Status:** active
- **Owners:** UI Platform Team

## Summary

Azure DevOps pipeline `pipelines/deploy.yml` triggered by the successful completion of the `IntegrateCoreUiNext` validate pipeline on `main`. Publishes all `@integrate-core-ui/*` packages to the ADO `rainier-latest` npm feed and deploys static assets (Storybook, sandbox-01, cookie-consent UMD) to Azure Blob Storage.

## Behavior

- Triggers: `trigger: none`, `pr: none`. Runs via pipeline resource trigger on successful `IntegrateCoreUiNext` validate pipeline + source branch `refs/heads/main` (`:4-12`). All stages guard on `Build.Reason == ResourceTrigger`.
- **Stage 1 — `Publish_IntegrateCoreUiNext`** (ubuntu-latest, `:19-87`)
  - Install + build full pnpm workspace + `idm-adapter`.
  - `npm@1` task with `customFeed: rainier-latest` and `command: publish` via `pnpm run publish:feed` (`:75-76`).
  - Separately publishes `idm-adapter` via `tools/publish-feed.ts` against its dist output (`:86-87`).
- **Stage 2 — `Deploy_Static_Webpages`** (windows-latest, `:89-155`)
  - Build Storybook (`pnpm --filter storybook-host run storybook:build`) → `AzureFileCopy@3` to `ltssharedassets/$web/IntegrateCoreUiNext/storybook`.
  - Build `sandbox-01` (`pnpm --filter sandbox-01 run build`) → `AzureFileCopy@3` to `ltssharedassets/$web/IntegrateCoreUiNext/web`.
  - Azure subscription: `Ops - integrate-solutions.com`.
- **Stage 3 — `Deploy_CookieConsent`** (windows-latest, `:157-205`)
  - Build `@integrate-core-ui/cookie-consent` → `AzureFileCopy@5` of `cookieConsent.js` to `legalassets/$web`.

## Entry Points

- `pipelines/deploy.yml:1` — triggers + pipeline resource declaration.
- `pipelines/deploy.yml:19-87` — `Publish_IntegrateCoreUiNext`.
- `pipelines/deploy.yml:75-76` — feed publish step.
- `pipelines/deploy.yml:86-87` — idm-adapter publish via `publish-feed.ts`.
- `pipelines/deploy.yml:89-155` — `Deploy_Static_Webpages`.
- `pipelines/deploy.yml:134` — Storybook upload path.
- `pipelines/deploy.yml:157-205` — `Deploy_CookieConsent`.

## Key Dependencies

- Azure DevOps tasks: `npm@1`, `AzureFileCopy@3`, `AzureFileCopy@5`, `npmAuthenticate@0`.
- ADO feed `rainier-latest`.
- Azure Blob Storage accounts `ltssharedassets` and `legalassets`.
- `tools/publish-feed.ts`.

## Related Features

- [ADO Validate Pipeline](./ado-validate-pipeline.md)
- [Publish Feed Script](./publish-feed-script.md)
- [Storybook Host](./storybook-host.md)
- [Cookie Consent Banner (business)](../business/cookie-consent.md)

## Change Log

- 2026-04-21: Seeded.
