# Localized UI Text

- **Category:** dev
- **Status:** active
- **Owners:** desktop-ui

## Summary

Translation infrastructure and lint enforcement for Integrate UI strings. The dev-side feature covers the `@repo/translations` catalogs, TypeScript typings, and the ESLint rule that prevents new unlocalized JSX text.

This is the developer-facing side of the localization work. For the user-visible behavior, see [Localized UI Text (business)](../business/localized-ui-text.md).

## Behavior

- English translation catalogs are composed into the `en` export and typed through `Translations`.
- `ComponentsTranslations.json` holds reusable component-library and Copilot Chat strings outside project-specific catalogs.
- The `no-unlocalized-strings` ESLint rule is enforced as an error so new JSX copy must use translation keys.
- Project Manager and shared component packages import `LocalizedStrings` instead of embedding literal UI copy.

## Entry Points

- `monorepo/desktop/ui/packages/translations/src/locales/en/index.ts`.
- `monorepo/desktop/ui/packages/translations/src/locales/types/index.ts`.
- `monorepo/desktop/ui/packages/eslint-presets/src/eslint-config.ts`.
- `monorepo/desktop/ui/packages/translations/src/locales/en/*.json`.

## Key Dependencies

- `@repo/translations`.
- ESLint preset package.
- React packages under `monorepo/desktop/ui/`.

## Related Features

- [Storybook](storybook.md)
- [CI Pipelines](ci-pipelines.md)

## Change Log

- 2026-05-13: PR #9685 feat: migrate all JSX unlocalized strings to `@repo/translations` and promote lint rule to error — added `ComponentsTranslations`, typed it into the catalog, migrated remaining JSX strings, and changed the lint rule from warning to error.
