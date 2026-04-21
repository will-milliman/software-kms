# CodeGenerator

- **Category:** dev
- **Status:** active
- **Owners:** mg-alfa / core

## Summary

A build-time code generator that emits serializer, DTO, and format boilerplate from declarative schema inputs, eliminating hand-written repetitive code across MG-ALFA/MMD/Projection components.

## Behavior

- Runs as a pre-build step (MSBuild target or explicit project).
- Consumes schema files under the source tree and writes generated `.cs` into `obj/Generated/`.
- Generated types are consumed by `Milliman.MGAlfa.Core` and peers.

## Entry Points

- `src/tools/CodeGenerator/` (approximate).
- `.targets`/`.props` that hook the build.

## Key Dependencies

- [GDF File Format Library](gdf-file-format.md).

## Related Features

- [UDF Code Generation](udf-code-generation.md)

## Change Log

- 2026-04-21: Seeded.
