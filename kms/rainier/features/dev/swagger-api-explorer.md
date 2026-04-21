# Swagger API Explorer (Project Manager)

- **Category:** dev
- **Status:** active
- **Owners:** backend-platform

## Summary

The locally-running Project Manager service exposes an interactive Swagger UI at `http://localhost:50202/swagger` for engineers and QA to browse and exercise its REST API.

## Behavior

- Enabled in all builds (dev and release) — Project Manager is trusted because it runs locally.
- OpenAPI spec is derived from controller annotations.
- Useful for reproducing UI operations at the API level and for exploring [Hypermedia (HAL) Responses](hypermedia-hal.md).

## Entry Points

- `src/server/Rainier.ProjectManager/Startup.cs` — Swagger registration.
- Root `README.md` documents the localhost URL.

## Key Dependencies

- `Swashbuckle.AspNetCore`.

## Related Features

- [Swagger API Explorer (business)](../business/swagger-api-explorer.md)
- [Hypermedia (HAL) Responses](hypermedia-hal.md)

## Change Log

- 2026-04-21: Seeded.
