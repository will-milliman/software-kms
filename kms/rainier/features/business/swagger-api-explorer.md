# Swagger API Explorer (Project Manager)

- **Category:** business
- **Status:** active
- **Owners:** authoring-and-governance

## Summary

Interactive API documentation for the Project Manager service, accessible while the local server is running. Exposed on `localhost:50202/swagger`. Used by both developers and business-side integrators to explore and exercise endpoints.

## Behavior

- Start the Project Manager server locally.
- Browse to `localhost:50202/swagger` to see all endpoints, request/response shapes.
- Click **Authorize** and paste `Bearer <jwt>` (obtain the JWT via `Ctrl+Shift+Alt+J` inside Integrate).
- Try endpoints live with authentication.

## Entry Points

- Root README documents the workflow: `README.md:355-366`.
- Swagger config registered in Project Manager startup (NSwag): `src/server/Rainier.ProjectManager/Rainier.ProjectManager.Server/Startup.cs` (`AddOpenApiDocument`).

## Key Dependencies

- NSwag (Project Manager, Acl, Governance).
- Swashbuckle (Origin, RunManager).

## Related Features

- [Swagger API Explorer (dev)](../dev/swagger-api-explorer.md) — dev counterpart.

## Change Log

- 2026-04-21: Seeded.
