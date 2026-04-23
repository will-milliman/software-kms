# cicd-shared Submodule

- **Category:** dev
- **Status:** active
- **Owners:** Platform Engineering

## Summary

The `cicd-shared/` directory is a git submodule pointing at `milliman-lts/cicd-shared` (branch `main`). It supplies the shared `make/help.mk` and `make/terraform.mk` files that implement the `validate_terraform`, `package`, `deploy`, etc. targets used by every stack.

## Behavior

- Declared in `.gitmodules:1` with `path = cicd-shared`, `url = https://github.com/milliman-lts/cicd-shared.git`, `branch = main`.
- Included from three places today:
  - `Makefile:3` — `include ./cicd-shared/make/help.mk` (root help target).
  - `ingress/make.mk:3` — `include ../cicd-shared/make/help.mk`.
  - `ingress/deploy/make.mk:1` — `include ../../cicd-shared/make/help.mk` and `../../cicd-shared/make/terraform.mk`.
- When cloning the repo, developers must `git submodule update --init --recursive`, or the makefiles will fail to include. (CI pipelines check out submodules by default with `checkout: self`.)

## Entry Points

- `.gitmodules:1`
- `Makefile:3`
- `ingress/make.mk:3`
- `ingress/deploy/make.mk:1-2`

## Key Dependencies

- Upstream repo: `milliman-lts/cicd-shared` (not cloned here; only referenced).
- [Make Command Protocol](make-command-protocol.md) — the shared targets are the primitives that the protocol exposes.

## Related Features

- [Make Command Protocol](make-command-protocol.md)
- [Containerised Toolchain](containerised-toolchain.md)

## Change Log

- 2026-04-21: Seeded.
