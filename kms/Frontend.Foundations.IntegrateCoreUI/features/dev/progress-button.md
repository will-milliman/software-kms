# Progress Button (`@integrate-core-ui/form`)

- **Category:** dev
- **Status:** active
- **Owners:** UI Platform Team

## Summary

A Carbon-based form widget that composes `Button` and `InlineLoading` to show submit progress, success, and error states. Currently the only component in the `@integrate-core-ui/form` library, with room to grow.

## Behavior

- Status machine: `inactive → active → finished | error → inactive` with a configurable `resultDelay` before resetting.
- Renders as a Carbon `Button` when inactive; swaps to a `StyledInlineLoading` for active/finished/error feedback.
- Emits `onClick` and exposes status / result props so parent forms can drive the state machine.

## Entry Points

- `packages/form/src/index.ts:1` — barrel export.
- `packages/form/src/progress-button/progress-button.tsx:15` — `ProgressButton` component.
- `packages/form/src/progress-button/progress-button.tsx:72` — `StyledInlineLoading`.
- `packages/form/src/progress-button/progress-button.test.tsx` — Vitest spec.
- `packages/form/src/progress-button/progress-button.stories.tsx` — Storybook story.

## Key Dependencies

- `@carbon/react` (peer) — `Button`, `InlineLoading`.
- `styled-components` (via `babel-plugin-styled-components`).
- React 19 (peer).

## Related Features

- [Storybook Host](./storybook-host.md)

## Change Log

- 2026-04-21: Seeded.
