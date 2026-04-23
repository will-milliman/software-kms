# api.foundations

- **Repo:** [milliman-lts/api.foundations](https://github.com/milliman-lts/api.foundations)
- **Default branch:** `main`
- **Seed date:** 2026-04-21
- **Topics:** azure, connectivity, platform-engineering
- **Lifecycle:** Incubating
- **Custodian:** Platform Engineering (GitHub team `milliman-lts/platform-engineering`)

## Purpose

`api.foundations` holds the Infrastructure-as-Code (Terraform + Azure DevOps pipelines) that provisions and manages the shared **API Foundation** components used across Milliman LTS engineering teams. Today this means the public-facing **API Ingress** stack: an Azure Front Door (Premium) profile, a WAF policy, a regional Azure API Management (APIM) tier, and the DNS Made Easy records that front them.

## Target audience

- **Platform Engineering** — owners who extend and operate the stack.
- **LTS product/engineering teams** — consumers who publish APIs behind the shared ingress.

## High-level capabilities

- Deploys a **global Azure Front Door Premium** profile with a custom domain (`api.<base_domain_name>`) and a customer TLS certificate from Key Vault.
- Attaches a **WAF policy** (Default Rule Set 2.1 + Bot Manager 1.1 in Log mode) with custom rules that block `TRACE` requests and sanctioned-country source IPs (BY, CN, CU, IR, KP, RU, SY).
- Provisions **per-region APIM Developer SKU** instances (for each region in `apim_regions`) with user-assigned managed identities, wired to Front Door as origins via a shared origin group and a single catch-all route.
- Publishes **DNS CNAMEs** to the APIM domain via the DNS Made Easy Terraform provider.
- Streams **diagnostic logs & metrics** from Front Door and APIM to both the Integrate Log Analytics workspace and the LTS Sentinel workspace.
- Runs everything through containerised tooling (Terraform, tflint, terraform-docs, Checkov, Conftest) defined in `docker-compose.yaml`, driven by a shared Makefile protocol.
- Ships an Azure DevOps pipeline (`ingress/.pipelines/iac-deploy.yaml`) that validates, plans, and packages the stack using reusable templates under `shared/azdo-templates/`.

## Repository shape

```
.
├── Makefile                          # Root entry point; dispatches to component make files
├── auth.sh                           # Developer Azure CLI login helper
├── docker-compose.yaml               # Toolchain container definitions
├── env-local.mk.tpl                  # Template for local developer env overrides
├── cicd-shared/                      # Git submodule: milliman-lts/cicd-shared (make helpers)
├── shared/azdo-templates/            # Reusable ADO pipeline steps (validate / package / deploy)
└── ingress/                          # The sole component stack today
    ├── .pipelines/iac-deploy.yaml    # ADO pipeline for the ingress stack
    ├── configuration/                # Per-env tfvars (*.json) and backend configs (*.backend.hcl)
    ├── deploy/                       # Root Terraform module
    │   ├── modules/regional_apim/    # Per-region APIM + AFD origin child module
    │   └── tests/checks.rego         # Conftest policy stub
    └── make.mk                       # Component-level make dispatcher
```

## Non-goals / not in this repo

- No application code — this repo provisions infra only.
- No API definitions or APIM policies are deployed here; only the APIM instances themselves.
- No production deploy stage is currently enabled (the `*_deploy` stage in the pipeline is commented out; see `ingress/.pipelines/iac-deploy.yaml:97`).
