# Features Index — app.foundations.trafficmanagement

## Business features

User- and consumer-team-visible capabilities.

| Feature | Summary |
|---|---|
| [SSL Certificate Issuance & Renewal](business/ssl-certificate-issuance-and-renewal.md) | DigiCert OV certificates auto-issued and auto-renewed for LTS domains. |
| [Domain Onboarding](business/domain-onboarding.md) | Add a new public domain to the hub and get a managed cert from day one. |
| [DCV Automation](business/dcv-automation.md) | Domain Control Validation closed end-to-end via DNS TXT — no humans pasting records. Cross-link: [dev DCV Watchdog Function](dev/dcv-watchdog-function.md). |
| [Hub-Spoke Cert Distribution](business/hub-spoke-cert-distribution.md) | Certs issued in the hub KV land automatically in every registered spoke KV. Cross-link: [dev CertSync Functions](dev/cert-sync-functions.md). |
| [Global App Ingress (Azure Front Door)](business/global-app-ingress-frontdoor.md) | Shared Azure Front Door Premium edge in front of LTS apps. |
| [Shared API Credentials (atmsec)](business/shared-api-credentials.md) | Central vault for DigiCert + DNS Made Easy API credentials. |
| [Cert Lifecycle Observability](business/cert-lifecycle-observability.md) | Sentinel + Log Analytics + metric alerts on every KV and function. |
| [Multi-Environment Spoke Provisioning](business/multi-env-spoke-provisioning.md) | Ship the spoke to `apidev`, `dev`, `gitopsdev`, `gitopsprod` (and any new env) via config-only changes. |
| [Spoke Onboarding Runbook](business/spoke-onboarding-runbook.md) | Documented procedure to register a new spoke with the hub for cert distribution. |

## Dev features

Build, CI, tooling, internal developer experience.

| Feature | Summary |
|---|---|
| [Terraform Four-Stack Layout](dev/terraform-four-stack-layout.md) | `atmsec` + `domain-management` + `ssl-cert-spoke` + `global-app-ingress` — independently deployable. |
| [Three Musketeers Build](dev/three-musketeers-build.md) | `make → docker compose → container`. No host-installed tools. |
| [Shared KeyVault Module](dev/shared-keyvault-module.md) | RBAC-authorized KV with Sentinel + LA diagnostics. |
| [Shared Flex Function-App Module](dev/shared-flex-function-module.md) | Flex Consumption (`FC1`) function app with blob-deploy + metric alerts. |
| [DCV Watchdog Function](dev/dcv-watchdog-function.md) | Python timer function driving the DCV state machine. Cross-link: [business DCV Automation](business/dcv-automation.md). |
| [CertSync Functions](dev/cert-sync-functions.md) | Queue-trigger + timer Python functions that sync hub certs to spokes. Cross-link: [business Hub-Spoke Cert Distribution](business/hub-spoke-cert-distribution.md). |
| [DCV State Machine](dev/dcv-state-machine.md) | `DomainState` × `DomainAction` dispatch table + action handlers. |
| [DigiCert v2 API Client](dev/digicert-v2-client.md) | Python client for DigiCert CertCentral v2. |
| [DNS Made Easy API Client](dev/dns-made-easy-client.md) | HMAC-SHA1 authenticated DME client for DNS record CRUD. |
| [Azure DevOps OIDC Pipelines](dev/azure-devops-oidc-pipelines.md) | Infra-cd + function-cd per stack, using federated identity (no SP secrets). |
| [OPA/Conftest + Checkov Gates](dev/opa-conftest-checkov-gates.md) | Policy-as-code compliance gates on every plan. |
| [Mandatory Tagging Policy](dev/mandatory-tagging-policy.md) | Eight platform tags required on every resource; enforced by Conftest. |
| [OpenTelemetry Instrumentation](dev/otel-instrumentation.md) | `azure-monitor-opentelemetry` + `@traced` decorator for function observability. |
| [Copilot Agents & Skills](dev/copilot-agents-and-skills.md) | `AGENTS.md`, `.github/agents/`, `.github/skills/`, `.github/prompts/`, `.github/instructions/`. |
