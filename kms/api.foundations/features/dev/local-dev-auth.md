# Local Developer Auth

- **Category:** dev
- **Status:** active
- **Owners:** Platform Engineering

## Summary

`auth.sh` + a developer-local `env-local.mk` give contributors a one-command path to run the same Terraform workflow locally that CI runs, against the dev environment.

## Behavior

### `auth.sh`
1. `az login --tenant da280eb2-7328-4fc8-9521-154de96d70eb` — the Milliman LTS tenant.
2. `az acr login --name integratesolutions` — authenticates docker against the toolchain registry.
3. Exports `AZURE_ACCESS_TOKEN` via `az account get-access-token --resource=https://management.azure.com/` for downstream tooling that expects a raw bearer token.

Invoked from the root Makefile as `make auth` (`Makefile:21`).

### `env-local.mk` (per developer)
Copied from `env-local.mk.tpl`:

```make
export ENVIRONMENT=dev
export SKIP_DME_SECRETS=false
export USE_COMPOSE=true
```

- `ENVIRONMENT` controls which `ingress/configuration/<env>.{json,backend.hcl}` files load.
- `SKIP_DME_SECRETS=false` tells `make` to resolve DME creds from Key Vault (see [DME Secrets from Key Vault](secrets-from-keyvault.md)).
- `USE_COMPOSE=true` routes shared make targets through the `docker-compose` services.

`env-local.mk` is gitignored (`.gitignore:17`).

## Entry Points

- `auth.sh:1` — tenant login, ACR login, token export.
- `Makefile:21` — `auth` target.
- `env-local.mk.tpl` — template for local overrides.
- `.gitignore:17` — excludes `env-local.mk`.

## Key Dependencies

- Azure CLI on the developer's machine.
- Docker — needed for `USE_COMPOSE=true`.
- [DME Secrets from Key Vault](secrets-from-keyvault.md).
- [Containerised Toolchain](containerised-toolchain.md).

## Related Features

- [Make Command Protocol](make-command-protocol.md)
- [DME Secrets from Key Vault](secrets-from-keyvault.md)

## Change Log

- 2026-04-21: Seeded.
