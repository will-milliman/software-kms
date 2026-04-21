# Hypermedia (HAL) Responses

- **Category:** dev
- **Status:** active
- **Owners:** backend-platform

## Summary

REST endpoints return HAL+JSON representations via the internal `Milliman.Hal` library, embedding `_links` and `_embedded` sections so clients can navigate resources by relation rather than hardcoding URLs.

## Behavior

- Controllers wrap domain models in HAL resource types before serialization.
- The renderer uses the HAL links to follow actions (edit, delete, children) without URL-building.
- Supports content negotiation: clients that don't ask for HAL can still get plain JSON.

## Entry Points

- `Milliman.Hal` NuGet package consumed by server projects.
- Controllers under `src/server/Rainier.ProjectManager/Controllers/` etc.

## Key Dependencies

- `Milliman.Hal`.

## Related Features

- [Swagger API Explorer (Project Manager)](swagger-api-explorer.md)
- [MediatR CQRS Pipeline](mediatr-cqrs.md)

## Change Log

- 2026-04-21: Seeded.
