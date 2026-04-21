# Hono Mock API

- **Category:** dev
- **Status:** active
- **Owners:** @milliman-lts/frontend-platform-engineering

## Summary

A dedicated **Hono** (TypeScript) mock API packaged in a Docker image provides a shared, realistic backend for both Vitest integration tests and Playwright E2E tests in the tenant-admin ClientApp. It is launched on-demand by Testcontainers so tests do not need the .NET services running.

## Behavior

- `tenant-admin/src/ClientApp/test/mock-api/Dockerfile` builds the mock image.
- Testcontainers (npm) starts the container with the desired seed fixtures and returns a base URL that the ClientApp tests point at.
- The same mock is reused across unit integration and E2E runs — guaranteeing consistent behavior.

## Entry Points

- `tenant-admin/src/ClientApp/test/mock-api/` — source + Dockerfile.
- `tenant-admin/src/ClientApp/test/integration/` — integration consumers.
- `tenant-admin/src/ClientApp/e2e/` — E2E consumers.
- `tenant-admin/src/ClientApp/README.md:196-233` — usage documentation.

## Key Dependencies

- Hono (TypeScript web framework).
- Testcontainers 10.24.2 (npm).

## Related Features

- [React Client Testing Stack](./react-client-testing-stack.md)

## Change Log

- 2026-04-21: Seeded.
