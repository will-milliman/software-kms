# Make Command Protocol

- **Category:** dev
- **Status:** active
- **Owners:** Platform Engineering

## Summary

All developer and CI interactions go through a namespaced Make target protocol of the form `<stack>__<area>__<target>`. The root Makefile forwards to component makefiles, which in turn forward into per-directory makefiles that include shared helpers from the `cicd-shared` submodule.

## Behavior

- Root entry: `make ingress__<target>` dispatches to `ingress/make.mk`.
- Component entry: `make ingress__infra__<target>` dispatches to `ingress/deploy/make.mk`.
- Double-underscore pattern rules use `make`'s `%` matching: e.g., `ingress__%` matches `ingress__infra__validate_terraform` with `$* = infra__validate_terraform`.
- `.ONESHELL:` is enabled so multi-line recipes run in a single shell.
- `AGENT_OS` is used as the "running in Azure DevOps" sentinel; if set, a log line announces it.
- Local env overrides come from an optional, gitignored `env-local.mk` (template: `env-local.mk.tpl`).
- `USER_ID`/`GROUP_ID` are set to `1001:1001` when running under `.ONESHELL`/CI, otherwise to `id -u`/`id -g`, so docker-compose bind-mounts don't leave root-owned files behind locally (`ingress/make.mk:5`).

## Entry Points

- `Makefile:17` — `ingress__%` forwarder.
- `Makefile:21` — `auth` target.
- `ingress/make.mk:14` — `infra__%` forwarder.
- `ingress/deploy/make.mk:1` — includes `cicd-shared/make/help.mk` and `cicd-shared/make/terraform.mk`; defines stack-specific vars.
- `env-local.mk.tpl` — local override template.

## Key Dependencies

- GNU Make (host or container).
- [cicd-shared Submodule](cicd-shared-submodule.md) — provides `help.mk` and `terraform.mk`.
- [Containerised Toolchain](containerised-toolchain.md) — when `USE_COMPOSE=true`, shared make targets execute inside the compose services.

## Related Features

- [cicd-shared Submodule](cicd-shared-submodule.md)
- [Containerised Toolchain](containerised-toolchain.md)
- [Azure DevOps Pipeline (ingress)](ado-pipeline-ingress.md) — invokes `make <prefix>validate_terraform`, `make <prefix>package`, etc.

## Change Log

- 2026-04-21: Seeded.
