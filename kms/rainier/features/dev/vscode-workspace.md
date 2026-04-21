# VS Code Workspace

- **Category:** dev
- **Status:** active
- **Owners:** devex

## Summary

`rainier.code-workspace` is a multi-root VS Code workspace definition that opens all relevant sub-trees (backend, MG-ALFA, MMD, Projection Execution, Desktop UI, Cloud, Tools) with consistent folder names, settings, and recommended extensions.

## Behavior

- Opening the `.code-workspace` file gives an engineer a pre-configured view of the mono-repo.
- Per-folder settings tune lint/format behavior for the dominant language in that folder.
- `extensions.recommendations` nudges new engineers to install the right toolchain.

## Entry Points

- `rainier.code-workspace` at repo root.

## Key Dependencies

- VS Code.

## Related Features

- [ADRs & How-To Docs](adrs-and-how-to.md)

## Change Log

- 2026-04-21: Seeded.
