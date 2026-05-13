# Localized UI Text

- **Category:** business
- **Status:** active
- **Owners:** desktop-ui

## Summary

User-facing Integrate UI copy is maintained through shared translation catalogs so labels, buttons, modal instructions, and grid text can be reviewed and prepared for localization instead of being embedded directly in components.

This is the customer-visible side of the localization work. For the linting and translation-catalog mechanics, see [Localized UI Text (dev)](../dev/localized-ui-text.md).

## Behavior

- Common UI strings such as `Save`, `Cancel`, `Project`, `Branch`, loading states, and validation labels are sourced from translation JSON files.
- Project Manager flows including ETL item creation, reference-file selection, sharing, project notes, promotions, and run details use localized string keys.
- Component-library Storybook examples and Copilot Chat labels also draw from shared translation resources.

## Entry Points

- `monorepo/desktop/ui/packages/translations/src/locales/en/CommonTranslations.json`.
- `monorepo/desktop/ui/packages/translations/src/locales/en/ProjectTranslations.json`.
- `monorepo/desktop/ui/packages/translations/src/locales/en/ComponentsTranslations.json`.
- `monorepo/desktop/ui/apps/web/src/components/`.
- `monorepo/desktop/ui/packages/project-manager/src/components/`.
- `monorepo/desktop/ui/packages/components/src/`.

## Key Dependencies

- `@repo/translations`.
- React renderer and Project Manager UI packages.

## Related Features

- [Feature Toggles](feature-toggles.md)
- [In-app AI Assistant](in-app-ai-assistant.md)

## Change Log

- 2026-05-13: PR #9685 feat: migrate all JSX unlocalized strings to `@repo/translations` and promote lint rule to error — moved remaining hard-coded UI text into translation catalogs and added component-level translations.
