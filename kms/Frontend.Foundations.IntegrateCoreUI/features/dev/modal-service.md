# Modal Service (`@integrate-core-ui/modal`)

- **Category:** dev
- **Status:** active
- **Owners:** UI Platform Team

## Summary

A thin wrapper around `@ebay/nice-modal-react` that registers modals in a central service, decorates the `create` HOC to inject a React error context, and ships a pre-styled `Modal` component built on Carbon's `ComposedModal`.

## Behavior

- `ModalService.create(Component)` replaces NiceModal's `create` — it wraps the component so that `ModalService.useModal()` returns the NiceModal handler merged with `{ error, setError }` from an internal `ErrorContext`.
- `<ModalService.Provider>` (aliased as `ModalProvider` / `IcuModalProvider`) wraps the app and owns the modal stack.
- `Modal` (`forwardRef`) is a Carbon `ComposedModal` with sensible `defaultModalProps`.
- `ModalServiceError` and `ModalServiceType` types support typed error handling; `ModalServiceHandler` unifies the handler shape.
- A `modalsReducer` (alias `IcuModalsReducer`) is exported for apps that wire NiceModal's reducer into Redux.

## Entry Points

- `packages/modal/src/index.ts:1-2` — `modal-service`, `modal`.
- `packages/modal/src/modal-service.tsx:21` — `ModalService`; `ErrorContext` (`:23`); custom `create` (`:30`); custom `useModal` (`:52`).
- `packages/modal/src/modal.tsx:12` — `Modal` (forwardRef over Carbon `ComposedModal`) + `defaultModalProps`.

## Key Dependencies

- `@ebay/nice-modal-react@1.2.13` (peer).
- `@carbon/react` (peer) — `ComposedModal`.
- React 19 (peer).

## Related Features

- [Tenant Management](../business/tenant-management.md) — uses the Modal Service extensively (Add/Delete/Import, Sign-Out).
- [Theme Builder](../business/theme-builder.md) — Export Modal.

## Change Log

- 2026-04-21: Seeded.
