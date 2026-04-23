# Azure DevOps Pipeline (ingress)

- **Category:** dev
- **Status:** active (validate + package only; deploy commented out)
- **Owners:** Platform Engineering

## Summary

`ingress/.pipelines/iac-deploy.yaml` is the Azure DevOps pipeline that validates and plans the ingress Terraform stack on every commit to `main` and every PR targeting `main` that touches `ingress/**` or `docker-compose.yaml`. The deploy stage is scaffolded but currently disabled.

## Behavior

- **Triggers:** CI on `main` and PR on `main`, path-filtered to `ingress/**` + `docker-compose.yaml`, excluding `ingress/README.md` and `ingress/docs/**`.
- **Agent pool:** `ubuntu-latest`.
- **Parameter:** `planOnly` (boolean, default `false`) — reserved for when the deploy stage is re-enabled.
- **Variables:** `stack=ingress`, `iac_path=$(Build.SourcesDirectory)/ingress/deploy`, `make_prefix=ingress__infra__`, service connections for dev / prod reader / prod owner.
- **Stages (active):**
  1. `tf_validate` — uses `shared/azdo-templates/validate-iac-template.yaml` with the dev service connection.
  2. `dev_package` — uses `shared/azdo-templates/package-iac-template.yaml`; publishes the Terraform plan (`ingress-dev.tfplan`), plan JSON, change summary, and Checkov JUnit results.
- **Stage (commented out):** `dev_deploy` — would download the plan and call the deploy template in ADO environment `identity-foundations-ingress-dev` with a 120-minute timeout.

## Entry Points

- `ingress/.pipelines/iac-deploy.yaml:1` — triggers and PR paths.
- `ingress/.pipelines/iac-deploy.yaml:29` — `planOnly` parameter.
- `ingress/.pipelines/iac-deploy.yaml:59` — stages definition.
- `ingress/.pipelines/iac-deploy.yaml:97` — commented `dev_deploy` stage.

## Key Dependencies

- [Reusable ADO IaC Templates](reusable-azdo-templates.md).
- [Make Command Protocol](make-command-protocol.md) — every inline script boils down to `make <prefix><target>`.
- ADO service connections: `integrate Application Owner Dev`, `integrate Application Reader`, `integrate Application Owner`.

## Related Features

- [Reusable ADO IaC Templates](reusable-azdo-templates.md)
- [Checkov Plan Scanning](checkov-scanning.md)
- [Terraform State Backend Convention](terraform-state-backend.md)

## Change Log

- 2026-04-21: Seeded.
