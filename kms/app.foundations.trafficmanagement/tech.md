# app.foundations.trafficmanagement — Technology Stack

## Languages & Runtimes

| Language | Where | Notes |
|---|---|---|
| **HCL / Terraform** | `atmsec/deploy/**`, `domain-management/deploy/**`, `ssl-cert-spoke/deploy/**`, `global-app-ingress/deploy/**`, `shared/modules/**` | Primary IaC language. Four independent stacks + two shared modules. |
| **Python 3.12** | `domain-management/functions/dcv-watchdog/src/**`, `ssl-cert-spoke/functions/cert-sync/src/**` | Azure Functions runtime. Timer + Queue triggers. |
| **Makefile** | root `Makefile`, per-stack `make.mk`, `shared/terraform.mk`, `cicd-shared/make/help.mk` | Three Musketeers orchestration. |
| **Shell** | `auth.sh`, `logout.sh` | Azure CLI login helpers. |
| **PowerShell** | `.pipelines/**` step scripts | Pipeline glue. |
| **Dockerfile** | (implicit — images pulled from ACR, not built here) | No images built; all tool images consumed as pre-built. |
| **Rego / OPA** | `shared/policy/**` (consumed via conftest) | Compliance policies run during `check_compliant`. |
| **Go Template** | Helm-ish fragments in tool configs | Minor. |

## Terraform

- **Required version:** `>= 1.14.5`.
- **Primary providers:**
  - `hashicorp/azurerm ~> 4.66` (most stacks)
  - `hashicorp/azuread ~> 3.8` (domain-management)
  - `DNSMadeEasy/dme ~> 1.0.8` (domain-management)
  - `hashicorp/dns ~> 3.5`
  - `hashicorp/time ~> 0.13.1`
  - `hashicorp/random` (atmsec)
- **Backend:** `azurerm` with `use_azuread_auth = true`.
  - Tenant: `da280eb2-7328-4fc8-9521-154de96d70eb`.
  - Hub state subscription: `cc31f800-80a8-4e8b-9cf5-a20a232a5b39`.
- **Per-env configuration:** `configuration/<env>.json` per stack (e.g. `dev`, `prod`, `apidev`, `gitopsdev`, `gitopsprod`).
- **Shared modules:**
  - `shared/modules/KeyVault/` — RBAC-authorized KV with Sentinel + LA diagnostic settings.
  - `shared/modules/flex-function-app/` — Flex-consumption (SKU `FC1`) Function App with SystemAssigned identity, `WEBSITE_RUN_FROM_PACKAGE` via blob, CORS for Azure portal, IP restriction to AzureCloud service tag, metric alerts.

## Azure Functions (Python)

- **Runtime:** Python 3.12 on `azurerm_function_app_flex_consumption` (SKU `FC1`, Linux).
- **Deploy:** container-built zip uploaded to blob, referenced via `WEBSITE_RUN_FROM_PACKAGE`.
- **Identity:** SystemAssigned managed identity.
- **Observability:** `azure-monitor-opentelemetry` auto-instrumentation. OTEL spans on handlers/actions via `@traced` decorator.
- **Functions in this repo:**
  - **DcvWatchdogFunction** (`domain-management/functions/dcv-watchdog/`) — Timer trigger `0 0 * * *` (daily). Drives domain DCV state machine.
  - **CertSyncFunction** (`ssl-cert-spoke/functions/cert-sync/src/CertSyncFunction/`) — Queue trigger on `cert-sync-events`. Syncs single cert from hub → spoke.
  - **FullSyncFunction** (`ssl-cert-spoke/functions/cert-sync/src/FullSyncFunction/`) — Timer trigger `%FULL_SYNC_SCHEDULE%`. Catches up missed events.
- **Shared function libs:**
  - `dcv-watchdog/src/DcvWatchdogFunction/clients/` — `DigiCertClientV2` (v2 REST, `X-DC-DEVKEY` auth), `DNSMadeEasyClient` (HMAC-SHA1 auth), `KeyVaultClient` (DefaultAzureCredential).
  - `cert-sync/src/shared/` — `config.py`, `keyvault_client.py`, `sync_logic.py`.

## Third-party APIs

| API | Where | Auth |
|---|---|---|
| **DigiCert CertCentral v2** | `dcv-watchdog/src/.../clients/digicert_client_v2.py` | `X-DC-DEVKEY` header (API key from Key Vault). |
| **DNS Made Easy** | `dcv-watchdog/src/.../clients/dme_client.py` | HMAC-SHA1 signature of HTTP date with secret key. |
| **Azure ARM / Key Vault / Storage / Event Grid** | All stacks | `DefaultAzureCredential` (workload identity in Azure, OIDC in pipelines, AZ CLI locally). |

## Build & Deploy tooling (Three Musketeers)

- **Root `Makefile`:** dispatches by stack prefix.
  - `atmsec__<target>` → `atmsec/make.mk`
  - `domain__<target>` → `domain-management/make.mk`
  - `spoke__<target>` → `ssl-cert-spoke/make.mk`
  - `gai__<target>` → `global-app-ingress/make.mk`
- **Common Terraform targets** (from `shared/terraform.mk`):
  - `infra__format_check`, `infra__validate_terraform`, `infra__lint_terraform`, `infra__plan`, `infra__plan_json`, `infra__check_compliant` (Checkov + Conftest), `infra__deploy`, `infra__destroy`.
- **Common function targets:**
  - `app__code_checks` (ruff + black), `app__unit_test` (pytest), `app__build` (zip package for `WEBSITE_RUN_FROM_PACKAGE`).
- **Docker images (from `integratesolutions.azurecr.io`):**
  - `terraform:1.14.5`, `tflint`, `checkov:3`, `conftest`, `trivy`, `terraform-change-annotate`, `powershell`.
- **`docker-compose.yaml`** at the repo root mounts the source tree into these images so tools never run on the host.
- **Auth helpers:** `auth.sh` / `logout.sh` for interactive Azure CLI + ADO PAT login into the container stack.

## CI/CD (Azure DevOps Pipelines)

- Per-stack pipelines under `<stack>/.pipelines/`:
  - `infra-cd.yaml` (or `infra-deploy.yaml` in `global-app-ingress`) — terraform plan/apply.
  - `function-cd.yaml` — Python tests, package, deploy zip to blob, trigger function restart.
- **Shared pipeline templates:** `shared/.pipelines/templates/{validate,package,deploy,destroy}-iac-template.yaml`.
- **Auth:** Azure DevOps **OIDC** federated identity (`ARM_USE_OIDC=true`). No stored client secrets.
- **Submodule:** `cicd-shared` → `https://github.com/milliman-lts/cicd-shared.git` (provides `make/help.mk` for self-documenting make help).

## Compliance & Security gates

- **Checkov 3** — infra static analysis per plan.
- **Conftest + OPA/Rego** — custom policies under `shared/policy/`, run against the terraform plan JSON.
- **TFLint** — HCL lint.
- **Trivy** — image/secret scanning (image pinned in tooling).
- **Mandatory tags** enforced on every resource via per-stack `tags.tf`:
  - `tagging-version`, `system-name`, `system-owner`, `system-domain`, `environment-name`, `environment-owner`, `environment-cost-center`, `environment-data-classification`.

## Observability

- **Azure Monitor OpenTelemetry** (`azure-monitor-opentelemetry`) — auto-instrumentation of function host.
- **Custom spans** via `@traced` decorator in `dcv-watchdog/src/DcvWatchdogFunction/instrumentation/`.
- **Diagnostic settings** (via shared KeyVault module) — all KV operations streamed to both a Sentinel workspace and a regional Log Analytics workspace.
- **Metric alerts** on Flex function apps — provisioned by `shared/modules/flex-function-app/metric_alerts.tf`.

## AI agents & Copilot tooling

- `.github/copilot-instructions.md` — scoped Copilot rules for the repo.
- `.github/copilot-sources.md` — allowed doc sources.
- `.github/agents/*.agent.md` — 5 persona agents (e.g. terraform reviewer, python reviewer, runbook author).
- `.github/skills/*/SKILL.md` — 14 reusable skills (e.g. terraform-plan-triage, function-deploy-debug).
- `.github/prompts/*.prompt.md` + `.github/instructions/*.instructions.md` — scoped instructions for specific tasks.
- `AGENTS.md` at the repo root — top-level agent guide.
