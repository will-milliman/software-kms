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
- 2026-05-11: PR #9725 chore: configure dependabot for monorepo with major/minor split — updated workspace folders/settings alongside Dependabot monorepo setup.
- 2026-05-11: PR #9727 fix: remove duplicate ecosystem-directory dependabot configurations — refined workspace YAML handling for Dependabot config work.
- 2026-05-11: PR #9750 chore: add ab link and update pip dependabot grouping and schedule — updated workspace schema handling for Dependabot YAML files.
- 2026-05-11: PR #9763 feature: add dependabot PR auto complete action — added GitHub workflow YAML schema coverage for the new Dependabot automation workflow.
- 2026-05-12: PR #9765 fix: ab link adding workflow does not run on PR edit — added the GitHub Actions VS Code extension recommendation alongside Azure Pipelines tooling.
