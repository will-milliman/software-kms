# DME Secrets from Key Vault

- **Category:** dev
- **Status:** active
- **Owners:** Platform Engineering

## Summary

DNS Made Easy API credentials are fetched just-in-time from an Azure Key Vault at make time and exported as environment variables for the Terraform DME provider. No DME secrets are stored in the repo, in Terraform state, or in ADO secret variables.

## Behavior

- In `ingress/deploy/make.mk:16`, when `SKIP_DME_SECRETS` is not `true`:
  - `ENV_MONIKER` = `dev` when `ENVIRONMENT=plt`, else `ENVIRONMENT`. (`plt` piggybacks on the `dev` vault.)
  - `DME_API_KEY` := `az keyvault secret show --name dme-api-key --vault-name connsec-${ENV_MONIKER} --query value -o tsv`
  - `DME_SECRET_KEY` := same, with secret name `dme-secret-key`.
- The `docker-compose.yaml` `terraform` service declares `DME_API_KEY` and `DME_SECRET_KEY` with no default, so they must be present in the caller's environment at `docker compose run` time (`docker-compose.yaml:14`).
- The `dme` Terraform provider block is intentionally empty; setting attributes would force validation that would fail because credentials come from env vars (`ingress/deploy/provider.tf:42`).

## Entry Points

- `ingress/deploy/make.mk:16` — env-gated secret resolution.
- `docker-compose.yaml:14` — compose passes the vars through.
- `ingress/deploy/provider.tf:42` — provider block rationale.

## Key Dependencies

- Azure Key Vault `connsec-dev` / `connsec-<env>`.
- Azure CLI (developer or pipeline SPN) with `Key Vault Secrets User` on the vault.
- `dnsmadeeasy/dme` Terraform provider.

## Related Features

- [Local Developer Auth](local-dev-auth.md)
- [DNS Made Easy Record Publishing](../business/dns-management.md)

## Change Log

- 2026-04-21: Seeded.
