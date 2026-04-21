# Dev CLI Tools

- **Category:** dev
- **Status:** active
- **Owners:** devex

## Summary

A set of auxiliary command-line tools used by engineers for release engineering, diagnostics, and content creation: AppInsightsClient, DevOpsClient, IconMaker, ScreenRecorder, and other support apps.

## Behavior

- AppInsightsClient queries telemetry for specific users/sessions during support cases.
- DevOpsClient wraps Azure DevOps REST calls for bulk queries and work-item updates.
- IconMaker generates the Integrate app-icon set from source SVGs.
- ScreenRecorder captures short clips for bug reports and docs.

## Entry Points

- `src/tools/` and `monorepo/tools/`.

## Key Dependencies

- Varies per tool (Azure.Monitor.Query, Azure.DevOps client libs, ImageSharp, etc.).

## Related Features

- [GitHub Release Automation](github-release-automation.md)

## Change Log

- 2026-04-21: Seeded.
