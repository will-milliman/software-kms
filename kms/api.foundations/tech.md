# Tech Stack

## Languages & file types

| Language / format | Where | Notes |
| --- | --- | --- |
| HCL / Terraform | `ingress/deploy/**/*.tf` | Primary IaC language |
| HCL backend config | `ingress/configuration/*.backend.hcl` | Azure Storage backend inputs |
| JSON (tfvars) | `ingress/configuration/*.json` | Per-environment variables |
| Makefile | `Makefile`, `ingress/make.mk`, `ingress/deploy/make.mk` | Command dispatch surface |
| Shell (bash) | `auth.sh`, inline pipeline scripts | Developer & CI glue |
| YAML | `docker-compose.yaml`, `ingress/.pipelines/iac-deploy.yaml`, `shared/azdo-templates/*.yaml` | Tooling + ADO pipelines |
| Rego (OPA) | `ingress/deploy/tests/checks.rego` | Conftest policy (stub today) |

## Runtimes & versions

- **Terraform:** `>= 1.14.5` (`ingress/deploy/provider.tf:2`). The container image pin is `integratesolutions.azurecr.io/integrate/tooling/terraform:1.14.5` (`docker-compose.yaml:5`).
- **Providers:**
  - `hashicorp/azurerm` `~> 4.66` — Azure resource provisioning (`ingress/deploy/provider.tf:12`).
  - `hashicorp/azuread` `~> 3.8` — reserved for AAD interactions (`ingress/deploy/provider.tf:17`).
  - `dnsmadeeasy/dme` `~> 1.0.8` — DNS record management (`ingress/deploy/provider.tf:22`).
- **Conftest:** `v0.62.0` container (`docker-compose.yaml:43`).
- **terraform-docs:** `0.20.0` container (`docker-compose.yaml:28`).
- **Checkov:** major version `3` container (`docker-compose.yaml:35`).

All tooling is fetched from the internal `integratesolutions.azurecr.io` registry (images are mirrored with upstream prefix paths such as `ghcr.io/...`, `docker.io/...`, `quay.io/...`).

## Azure resources created

Created by `ingress/deploy/`:

- `azurerm_resource_group.rg` — holds everything else (`ingress/deploy/rg.tf:1`).
- `azurerm_cdn_frontdoor_profile.afd` (Premium SKU, 240s response timeout) (`ingress/deploy/afd.tf:1`).
- `azurerm_cdn_frontdoor_endpoint.endpoint` (`ingress/deploy/afd.tf:10`).
- `azurerm_cdn_frontdoor_origin_group.api` (`ingress/deploy/afd.tf:17`).
- `azurerm_cdn_frontdoor_firewall_policy.domain` (WAF, Detection mode) (`ingress/deploy/afd_waf.tf:1`).
- `azurerm_cdn_frontdoor_custom_domain.domain` + `azurerm_cdn_frontdoor_secret.domain` + `azurerm_cdn_frontdoor_security_policy.domain` (`ingress/deploy/afd_domain.tf`).
- `azurerm_cdn_frontdoor_route.api` (`ingress/deploy/afd_route.tf:1`).
- `azurerm_monitor_diagnostic_setting.health_metrics` + `azurerm_monitor_diagnostic_setting.afd_sentinel` (`ingress/deploy/afd_diagnostics.tf`).
- `dme_dns_record.record` (CNAME to the AFD endpoint hostname) (`ingress/deploy/dns.tf:5`).

Per region via `module.regional_apim`:

- `azurerm_user_assigned_identity.apim_identity` (`ingress/deploy/modules/regional_apim/apim.tf:1`).
- `azurerm_api_management.apim_internal` — Developer_1 SKU, public access enabled on create (`ingress/deploy/modules/regional_apim/apim.tf:9`).
- `azurerm_cdn_frontdoor_origin.origin` pointing at the APIM gateway hostname (`ingress/deploy/modules/regional_apim/afd_origin.tf:9`).
- `azurerm_monitor_diagnostic_setting.apim_integrate` (`ingress/deploy/modules/regional_apim/diagnostics.tf:5`).

## State backend

- **Backend:** `azurerm` using Azure AD auth (`use_azuread_auth = true`).
- **Subscription:** `cc31f800-80a8-4e8b-9cf5-a20a232a5b39` (`lz.data-platform.infra.management`).
- **Tenant:** `da280eb2-7328-4fc8-9521-154de96d70eb`.
- **Container:** `tfstate`.
- **Resource group / account (dev & plt):** `infrastructure-automation-dev` / `devtfaccount85c403d0` (`ingress/configuration/{dev,plt}.backend.hcl`).
- **State key:** `api.foundations/${STACK_NAME}-${ENVIRONMENT}.tfstate` (`ingress/deploy/make.mk:8`).

## External services

- **DNS Made Easy** — DNS authority for `plt.integrate-solutions.com`. Credentials supplied via `DME_API_KEY` / `DME_SECRET_KEY` env vars pulled from the `connsec-<env>` Key Vault (`ingress/deploy/make.mk:17`).
- **Azure Log Analytics** — `integrate-monitoring-us` workspace for ops logs (`ingress/deploy/variables.tf:22`).
- **Azure Sentinel** — `ltssentinelsecurity` workspace for security logs (`ingress/deploy/variables.tf:38`).
- **Azure Key Vault** — holds the customer TLS certificate referenced by `base_domain_name_certificate_key_vault_id`.

## CI / CD

- **Platform:** Azure DevOps Pipelines.
- **Pipeline:** `ingress/.pipelines/iac-deploy.yaml` — triggers on `main` and PRs with path filters on `ingress/**` and `docker-compose.yaml`.
- **Agent pool:** `ubuntu-latest`.
- **Service connections:**
  - `integrate Application Owner Dev` (dev plans/validates)
  - `integrate Application Reader` (prod plan/read)
  - `integrate Application Owner` (prod deploy, not yet active)
- **Reusable steps** (`shared/azdo-templates/`):
  - `validate-iac-template.yaml` — `terraform fmt`/`validate`/`lint` (via `make <prefix>validate_terraform`).
  - `package-iac-template.yaml` — runs `make <prefix>package` and publishes the Terraform plan, plan JSON, change summary, and Checkov JUnit report as ADO artifacts.
  - `deploy-iac-template.yaml` — downloads the packaged plan and runs `make <prefix>deploy`.
- **Active stages** today: `tf_validate` then `dev_package` (plan + Checkov). The `dev_deploy` stage is commented out.

## Build / task protocol

Everything is invoked through `make` targets with a stack-prefixed namespace:

- `make ingress__infra__<target>` — drills through `Makefile` → `ingress/make.mk` → `ingress/deploy/make.mk`.
- Targets themselves (`validate_terraform`, `package`, `deploy`, etc.) live in the `cicd-shared` submodule at `cicd-shared/make/terraform.mk`.
- Local runs expect `USE_COMPOSE=true` and `ENVIRONMENT=dev` via `env-local.mk` (template at `env-local.mk.tpl`).

## Developer auth

`auth.sh` logs a developer into the `da280eb2-...` tenant, authenticates to the `integratesolutions` ACR, and exports `AZURE_ACCESS_TOKEN` for Terraform data sources that need a raw bearer token.
