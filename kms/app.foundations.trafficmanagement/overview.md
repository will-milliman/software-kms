# app.foundations.trafficmanagement

- **Repo:** [milliman-lts/app.foundations.trafficmanagement](https://github.com/milliman-lts/app.foundations.trafficmanagement)
- **Default branch:** `main`
- **Seed date:** 2026-04-21

## Purpose

`app.foundations.trafficmanagement` is the **Milliman LTS Platform Engineering** foundation for **Application Traffic Management (ATM)** on Azure. It provisions and automates the shared infrastructure that places SSL/TLS certificates, DNS records, and ingress (Azure Front Door) in front of LTS applications so product teams don't have to own any of that plumbing themselves.

The repo delivers:

- A **hub** that centrally issues and renews DigiCert OV certificates, automates **Domain Control Validation (DCV)** via DNS Made Easy, and stores certs in a hub Azure Key Vault.
- A **spoke** pattern that subscribes to the hub via Event Grid and keeps spoke-local Key Vaults in sync with newly-issued or renewing certificates.
- A **Global App Ingress** stack that stands up Azure Front Door Premium as the shared edge for LTS applications.
- A **shared secrets** stack (`atmsec`) that holds the DigiCert / DNS Made Easy API credentials used by the automation.

## Target audience

- **Platform / DevOps engineers** operating the ATM foundations (primary owners).
- **Product application teams** whose workloads consume the certificates, DNS records, and ingress routes provisioned here (mostly indirect — they onboard a domain and get auto-renewed certs).
- **Security / compliance** (cert lifecycle, tagging, RBAC evidence).

## High-level capabilities

- **Automated DigiCert OV certificate issuance & renewal** through DigiCert's v2 API, using DNS-based DCV.
- **DNS Made Easy (DME) integration** — programmatic CRUD of DNS records (TXT for DCV, plus domain management).
- **Hub/Spoke certificate distribution** — hub-issued certs flow to spokes via Event Grid + queue-triggered Azure Functions; late-joiners and missed events are caught by a timer-driven full sync.
- **DCV Watchdog** — a Python Azure Function (timer-triggered daily) that drives each domain through a state machine: *Add → Activate → SubmitForValidation → PollUntilValidated → Cleanup*. Handles pending-validation timeouts and TXT-record cleanup.
- **CertSync Functions** — queue-triggered per-event sync plus a timer-driven full sync on the spokes.
- **Azure Front Door Premium** as the global edge for LTS apps.
- **Shared Key Vault module** with mandatory diagnostic logging to Sentinel + Log Analytics, RBAC-authorized.
- **Flex-consumption Function App module** with deploy-via-blob (`WEBSITE_RUN_FROM_PACKAGE`), SystemAssigned identity, IP restriction to AzureCloud service tag, and metric alerts.
- **Three Musketeers build/deploy** — `make → docker compose → container` so all Terraform, linting, compliance, and Python tooling runs in pinned container images.
- **OPA/Conftest + Checkov compliance gates** in every Terraform plan.
- **Azure DevOps OIDC pipelines** — one infra-cd and one function-cd pipeline per stack, using federated identities (no stored service-principal secrets).
- **OpenTelemetry** instrumentation in the Python functions via `azure-monitor-opentelemetry`.

## Key tech

Terraform ≥ 1.14.5 (azurerm 4.x, azuread 3.x, DNSMadeEasy provider), Python 3.12 Azure Functions (`azure-functions`, `azure-keyvault-*`, `azure-identity`, `azure-monitor-opentelemetry`), Azure Key Vault, Azure Event Grid System Topics, Azure Storage Queues, Azure Front Door Premium, DigiCert CertCentral v2 API, DNS Made Easy API, OPA/Conftest, Checkov, TFLint, Trivy, Make, Docker Compose, Azure DevOps Pipelines (OIDC), pytest, ruff, black.

## Dominant languages (GitHub-reported)

Python (288,706 bytes), HCL/Terraform (81,575 bytes), Makefile (17,467), Shell, PowerShell, Dockerfile, Go Template, OPA/Rego.

## Topics

`certificates`, `dns`, `ingress`, `platform-engineering`.

## Stack status (self-reported in READMEs)

| Stack | Status |
|---|---|
| `atmsec/` | Generally Available |
| `domain-management/` | Incubating |
| `ssl-cert-spoke/` | Incubating |
| `global-app-ingress/` | Incubating |
