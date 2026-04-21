# Software Update Distribution

- **Category:** business
- **Status:** active
- **Owners:** @milliman-lts/platform-engineering

## Summary

`Integrate.SaaS.AppServices.UpdateService` serves **software update artifacts** (via byte-range / multipart downloads) and **release notes** (HAL+JSON, backed by YAML) to customer clients. Reached through the ingress at `/api/downloads/**` and `/api/releasenotes/**`.

## Behavior

- JwtBearer-protected; ring-scoped (a client only sees artifacts for its assigned ring).
- Range requests + multipart responses for resumable downloads (`UpdateService/Responses/MultipartResult.cs`).
- Release notes parsed from YAML with `YamlDotNet` and returned as HAL documents via `Milliman.Hal`.
- Artifacts stored in the `update-downloads` blob container.

## Entry Points

- `auth-server/src/Integrate.SaaS.AppServices.UpdateService/Program.cs:1-105` — host bootstrap.
- `auth-server/src/Integrate.SaaS.AppServices.UpdateService/Controllers/ReleaseNotesController.cs` — release notes.
- `auth-server/src/Integrate.SaaS.AppServices.UpdateService/Responses/MultipartResult.cs` — range/multipart.
- Ingress routes `downloads` + `release-notes` — `auth-server/src/Integrate.SaaS.AuthServer.Ingress/appsettings.json`.

## Key Dependencies

- `Microsoft.AspNetCore.Authentication.JwtBearer`.
- `Milliman.Hal`, `YamlDotNet`.
- Azure Blob Storage (`update-downloads`).

## Related Features

- [Ring-Based Rollout](./ring-based-rollout.md)
- [Tenant Configuration Service](./tenant-configuration-service.md)

## Change Log

- 2026-04-21: Seeded.
