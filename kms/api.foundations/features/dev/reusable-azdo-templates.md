# Reusable ADO IaC Templates

- **Category:** dev
- **Status:** active
- **Owners:** Platform Engineering

## Summary

Three step templates under `shared/azdo-templates/` encapsulate the validate / package / deploy lifecycle for any Terraform stack in this repo (and, by convention, future sibling repos). Each template authenticates via `AzureCLI@2` with `addSpnToEnvironment`, sets OIDC-flavored `ARM_*` variables, logs into the integrate ACR, and invokes a `make <prefix><target>` command.

## Behavior

### `validate-iac-template.yaml`
- Inputs: `identity`, `make_prefix`, `environment`.
- Exports `ARM_CLIENT_ID/OIDC_TOKEN/TENANT_ID/USE_OIDC`, `AZURE_ACCESS_TOKEN`, `SYSTEM_ACCESSTOKEN`, `ENVIRONMENT`.
- Calls `make ${make_prefix}validate_terraform`.

### `package-iac-template.yaml`
- Inputs: `artifact_prefix`, `identity`, `make_prefix`, `environment`, `stack_path`, `tf_plan_name`, optional `workload_type`.
- Runs `make ${make_prefix}package` under the same OIDC auth pattern.
- Publishes three pipeline artifacts: the binary plan (`<tf_plan_name>.tfplan`), the JSON plan (`<tf_plan_name>.tfplan.json`), and a Markdown change summary (`<tf_plan_name>.tfplan.md`).
- Publishes Checkov results as JUnit (`checkov-results.xml`) so ADO shows them on the Tests tab.

### `deploy-iac-template.yaml`
- Inputs: same set as package (minus the test publishing).
- Downloads the previously-published `<artifact_prefix>TfPlan` artifact, moves the file back into place at `<stack_path>/<tf_plan_name>.tfplan`, then runs `make ${make_prefix}deploy`.

## Entry Points

- `shared/azdo-templates/validate-iac-template.yaml:1`
- `shared/azdo-templates/package-iac-template.yaml:1`
- `shared/azdo-templates/deploy-iac-template.yaml:1`

## Key Dependencies

- Azure DevOps `AzureCLI@2` task with OIDC-capable service connections.
- [Make Command Protocol](make-command-protocol.md) — targets `validate_terraform`, `package`, `deploy` live in `cicd-shared/make/terraform.mk`.
- `integratesolutions` ACR — `az acr login` runs inline before every make call.

## Related Features

- [Azure DevOps Pipeline (ingress)](ado-pipeline-ingress.md)
- [Checkov Plan Scanning](checkov-scanning.md)

## Change Log

- 2026-04-21: Seeded.
