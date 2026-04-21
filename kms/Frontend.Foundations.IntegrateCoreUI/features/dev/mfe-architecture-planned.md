# Planned MFE Architecture

- **Category:** dev
- **Status:** planned
- **Owners:** UI Platform Team

## Summary

A documented but not-yet-implemented Module Federation strategy for Integrate. Captured across three Feb 2026 ADRs and a Nov 2025 spike: coarse-grained domain-aligned MFEs, **Rspack** as the Module Federation bundler (Vite's MF support is unreliable), route-based composition configured by the platform team, and pub/sub over props via `@integrate-core-ui/federation`.

## Behavior

- **Architecture** (`docs/decisions/[2026-02-16]-MFE-Architecture-Design.md`):
  - Remotes built with Rspack (not Vite) — Vite's MF plugin has issues with ingress/proxy, singleton sharing, `@integrate-core-ui` sharing, and ESM/CJS conflicts.
  - Coarse-grained, domain-aligned MFEs with a Platform team plus Vertical-feature teams.
  - Route-based composition: each MFE owns a URL prefix.
  - Platform team configures routes at design-time; MFEs handle their internal routing.
  - Pub/sub over props: host publishes context (user, tenant, feature flags, env) via `@integrate-core-ui/federation`; remotes consume read-only.
  - Minimal integration contract: React component + route + pub/sub subscription.
  - Open question (as of ADR): MF vs iframe — unresolved.
- **Operations** (`docs/decisions/[2026-02-16]-MFE-Development-Operations.md`):
  - Static hosting to Azure Blob `ltssharedassets/$web/OneIntegrate/remotes/<name>/`.
  - Host uses a static `mf-manifest.json`.
  - Deploy gated by an ADO `resources.pipelines` trigger on validate success.
  - **Integration Gate**: each remote's validate pipeline runs its own unit/E2E, then invokes a Host-owned reusable ADO template (`pipelines/templates/integration-gate.yml@host`) which boots the Host and runs Playwright smoke tests against a locally-served remote build, using a `REMOTE_<NAME>_MANIFEST_URL` env var override.
  - Breaking changes use **additive versioning**: producer ships v2 alongside v1, consumer validates, platform wires host to v2, deprecate v1 after migration.
- **Why Vite is not suitable** (`docs/decisions/[2026-02-16]-Vite-Module-Federation-Limitations.md`): Vite's native ESM loader bypasses MF's shared-scope interception; mixed CJS/ESM packages exacerbate this; Rspack works because it uses webpack's resolution pipeline that MF was designed for.
- **Spike findings** (`docs/spikes/[2025-11-24]-Module-Federation.md`): Proven with Vite PoC, shared ICUN + TanStack Router; pain points included outdated Nx templates, weak `@module-federation/vite` ecosystem, singleton tuning, CJS fallback — partially fixed by switching ICUN dist filenames from `index.cjs.js/index.mjs.js` to `index.cjs/index.mjs`. Spike recommended Vite long-term + evaluating Turborepo over Nx; the later Feb 2026 ADR then reversed to Rspack for MF specifically.

## Entry Points

- `docs/decisions/[2026-02-16]-MFE-Architecture-Design.md:1`.
- `docs/decisions/[2026-02-16]-MFE-Development-Operations.md:1`.
- `docs/decisions/[2026-02-16]-Vite-Module-Federation-Limitations.md:1`.
- `docs/spikes/[2025-11-24]-Module-Federation.md:1`.
- `packages/federation/src/index.ts:1` — the pub/sub primitive that will back host/remote context.

## Key Dependencies

- Rspack (target bundler; not yet in the repo).
- `@integrate-core-ui/federation` — existing pub/sub runtime.
- Azure Blob Storage `ltssharedassets` — planned remote hosting.

## Related Features

- [Federation Pub/Sub](./federation-pubsub.md)
- [ADO Validate Pipeline](./ado-validate-pipeline.md)
- [ADO Deploy Pipeline](./ado-deploy-pipeline.md)

## Change Log

- 2026-04-21: Seeded.
