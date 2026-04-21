# MMD Collaborative Development

- **Category:** business
- **Status:** active
- **Owners:** authoring-and-governance (+ model-development team)

## Summary

**Milliman Model Development (MMD)** lets teams of actuarial developers collaborate on shared MG-ALFA models with change management, proposal review, and live updates. It ships as two artifacts: the **Developer Desktop** (WPF client) and the **Developer Site** (ASP.NET + worker + Azure Functions server).

## Behavior

- Each developer runs Developer Desktop, which caches checked-out model files locally.
- On edit, diffs are computed and a proposal can be submitted.
- Reviews happen on Developer Site (web portal).
- SignalR pushes live updates (who's editing what, new proposals).
- Conflict resolution uses the same three-way merge engine as Integrate's Sync/Merge flow.

## Entry Points

- Desktop:
  - `monorepo/model-development/src/DiffMergeClient/DeveloperDesktop/App.xaml.cs`.
  - `.../DeveloperClientInstaller.cs` (17 KB — DI bootstrap).
  - `.../DevSiteApplicationLifecycle.cs`.
  - Subdirs: `Views/`, `ViewModels/`, `Services/`, `SignalR/`, `EditLaunchers/` (invokes MG-ALFA editors), `SyntaxEditor/`, `Cache/`.
  - Azure integration: `.../DeveloperDesktop.Azure/`, `.../DeveloperDesktop.Services/`.
  - Local file cache: `.../DesktopFileCache/`.
- Site:
  - `monorepo/model-development/src/Server/WebSite/` — ASP.NET web UI.
  - `.../Server/WorkerRole/` + `WorkerRoleHost/` — background workers.
  - `.../Server/ServerShared*/` — shared libs (`ServerShared`, `ServerShared.Azure`, `ServerShared.ES`, `ServerShared.RazorEngine`).
  - `.../Functions/` — Azure Functions (`Dockerfile.functions-diff`).
  - Solutions: `DeveloperSite.sln`, `DeveloperSiteServer.sln`.
- Pipelines: `monorepo/model-development/.pipelines/developer-desktop/*.yaml`, `.../.pipelines/developer-site/*.yaml`, `.../.pipelines/wkhtmltopdf/*.yaml`.

## Key Dependencies

- [Conflict Resolution Engine](../dev/conflict-resolution-engine.md).
- [GDF File Format Library](../dev/gdf-file-format.md).
- `wkhtmltopdf` container for PDF rendering (diffs, reports).
- Elasticsearch (`ServerShared.ES`).

## Related Features

- [MMD Conflict Resolution](mmd-conflict-resolution.md)
- [Conflict Resolution](conflict-resolution.md)
- [MG-ALFA Classic Editors](mg-alfa-classic-editors.md)

## Change Log

- 2026-04-21: Seeded.
