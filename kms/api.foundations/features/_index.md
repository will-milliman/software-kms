# Features

## Business features

User/customer-visible capabilities delivered by the API Foundation.

| Feature | Summary |
| --- | --- |
| [API Custom Domain & TLS](business/api-custom-domain.md) | Exposes `api.<base_domain_name>` with a customer-managed TLS certificate. |
| [Global Azure Front Door Ingress](business/global-front-door.md) | Premium AFD profile, endpoint, origin group, and route that terminate client traffic. |
| [WAF Policy & Geo Blocking](business/waf-policy.md) | Managed rule sets (logging) plus hard blocks on `TRACE` and sanctioned geographies. |
| [Regional APIM Gateways](business/regional-apim.md) | Per-region Azure API Management (Developer SKU) instances behind Front Door. |
| [DNS Made Easy Record Publishing](business/dns-management.md) | Authoritative CNAME from the custom domain to the AFD endpoint. |
| [Integrate Workspace Observability](business/observability-integrate.md) | Ops logs/metrics from AFD and APIM to the Integrate Log Analytics workspace. Also a [dev feature](dev/observability-integrate.md). |
| [Sentinel Security Logging](business/observability-sentinel.md) | AFD access and WAF logs forwarded to the LTS Sentinel workspace. |
| [HTTPS-only Enforcement](business/https-enforcement.md) | All HTTP requests are upgraded to HTTPS at Front Door; default AFD domain is disabled. |

## Dev features

Build, CI, tooling, and internal developer experience.

| Feature | Summary |
| --- | --- |
| [Make Command Protocol](dev/make-command-protocol.md) | `<stack>__<area>__<target>` namespace dispatched from the root Makefile. |
| [Containerised Toolchain](dev/containerised-toolchain.md) | Pinned Terraform/tflint/terraform-docs/Checkov/Conftest images in `docker-compose.yaml`. |
| [Azure DevOps Pipeline (ingress)](dev/ado-pipeline-ingress.md) | Validate + plan + package for the ingress stack on `main` and PRs. |
| [Reusable ADO IaC Templates](dev/reusable-azdo-templates.md) | Shared `validate` / `package` / `deploy` step templates under `shared/azdo-templates/`. |
| [Terraform State Backend Convention](dev/terraform-state-backend.md) | Azure Storage backend with AzureAD auth and `api.foundations/<stack>-<env>.tfstate` keys. |
| [cicd-shared Submodule](dev/cicd-shared-submodule.md) | Git submodule providing shared make helpers. |
| [Checkov Plan Scanning](dev/checkov-scanning.md) | JUnit-formatted IaC security scan published as a test result. |
| [Conftest Policy Harness](dev/conftest-policy.md) | Rego package stub wired for future plan-level assertions. |
| [Local Developer Auth](dev/local-dev-auth.md) | `auth.sh` + `env-local.mk` workflow for running Terraform locally. |
| [DME Secrets from Key Vault](dev/secrets-from-keyvault.md) | Just-in-time fetch of DME creds from `connsec-<env>` Key Vault at make time. |
| [Integrate Workspace Observability](dev/observability-integrate.md) | Diagnostics wiring consumed by engineers for debugging. Also a [business feature](business/observability-integrate.md). |
