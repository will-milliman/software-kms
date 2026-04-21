# app.foundations.trafficmanagement — Domain

## Glossary

| Term | Meaning |
|---|---|
| **ATM** | "Application Traffic Management." Umbrella name for certs + DNS + ingress in this repo. |
| **LTS** | Milliman's **Life Technology Solutions** business unit — the consumer of these foundations. |
| **Platform Engineering** | The team owning this repo. Produces shared Azure foundations for LTS product teams. |
| **Foundation** | A reusable platform-level capability (this repo = the "traffic management" foundation). |
| **Hub** | The central `domain-management` stack: hub Key Vault, DigiCert issuer, DCV automation, Event Grid source. One per environment. |
| **Spoke** | A consumer stack (`ssl-cert-spoke`). Owns its own Key Vault and subscribes to the hub's Event Grid topic to receive copies of issued certs. |
| **Hub Key Vault** | `sslcerthub-<env>` — the one KV where DigiCert-issued certs are stored and from which Event Grid events fire. |
| **Spoke Key Vault** | Per-env KV that receives a synced copy of the cert. Application workloads in that subscription read from here. |
| **DCV** | **Domain Control Validation**. DigiCert's process of confirming the applicant controls the domain. This repo uses the DNS-TXT DCV method exclusively. |
| **`_dnsauth.<domain>`** | The DNS TXT record name DigiCert expects for DNS-TXT DCV. |
| **DCV Token** | The random token DigiCert expects to find in the TXT record. Stored as a KV secret `<domain-with-dots-replaced-by-dashes>-dcv-token`. |
| **DCV Requested At** | Timestamp secret `<domain>-dcv-requested-at` used to detect pending-validation timeouts. |
| **Pending Validation Timeout** | `PENDING_VALIDATION_TIMEOUT_HOURS` (default 24) — if DigiCert hasn't validated in that window, the Watchdog resubmits. |
| **Cleanup TXT Period** | `CLEANUP_TXT_PERIOD_HOURS` (default 48) — age at which validated DCV TXT records are removed from DME. |
| **Expiry Threshold** | `DCV_EXPIRY_THRESHOLD_DAYS` (default 30) — cert is treated as "expiring" and re-validated. |
| **DME** | **DNS Made Easy** — Milliman's external DNS provider. All DNS records (including DCV TXTs and production domain records) are owned here. |
| **DigiCert CertCentral v2** | DigiCert's public REST API. Auth header: `X-DC-DEVKEY`. |
| **DigiCert Issuer** | An `azurerm_key_vault_certificate_issuer` resource telling the KV how to talk to DigiCert. Credentials come from `atmsec`. |
| **OV** | "Organization Validation" — the DigiCert cert product used here. Requires org vetting + DCV. |
| **Watchdog** | The DCV Watchdog Azure Function — closes the DCV loop so humans never need to paste TXT records. |
| **State Machine** | `DomainState` × `DomainAction` matrix in `domain_state.py`. Each Watchdog run computes current state and executes one action. |
| **Full Sync** | `FullSyncFunction` on the spoke — timer-triggered safety net that reconciles hub → spoke even if an Event Grid event was lost. |
| **Cert Sync Event** | A Storage Queue message (originally an Event Grid event) with subject `certificates/<name>` and event type `Microsoft.KeyVault.CertificateNewVersionCreated` or `CertificateNearExpiry`. |
| **Event Grid System Topic** | `hub-kv-cert-events-<env>` — the EG system topic on the hub KV. |
| **DLQ** | Dead-letter container on the spoke storage account for un-processable cert-sync queue messages. |
| **GAI** | **Global App Ingress** — the `global-app-ingress/` stack. Azure Front Door Premium edge. |
| **Three Musketeers** | The `make → docker compose → container` pattern. Nothing runs on the host — `terraform`, `python`, `black`, `ruff`, `checkov`, `conftest` all run in pinned container images. |
| **Make prefix** | Each stack has a prefix in the root Makefile: `atmsec__`, `domain__`, `spoke__`, `gai__`. Delegates to `<stack>/make.mk`. |
| **`atmsec`** | The shared-secrets stack. Holds DigiCert + DME API credentials for all other stacks. |
| **OIDC federated identity** | Azure DevOps ↔ Azure AD trust that replaces stored SP secrets. `ARM_USE_OIDC=true` in pipelines. |
| **Flex Consumption** | The Azure Functions hosting SKU used here (`FC1`). Pay-per-use Linux, scale-to-zero, `WEBSITE_RUN_FROM_PACKAGE` deploy model. |
| **`WEBSITE_RUN_FROM_PACKAGE`** | App setting pointing at a blob URL holding the zipped function code. Deploy = upload new zip + restart. |
| **Conftest / OPA / Rego** | Policy-as-code. `check_compliant` target runs Rego policies against `terraform plan -out -json`. |
| **Checkov** | Static-analysis security scanner for Terraform. Run in `check_compliant`. |
| **TFLint** | HCL linter. Also run in the common pre-plan target. |
| **Mandatory Tags (the 8)** | `tagging-version`, `system-name`, `system-owner`, `system-domain`, `environment-name`, `environment-owner`, `environment-cost-center`, `environment-data-classification`. |
| **Incubating / GA** | Self-reported stack maturity — stated in each stack's `readme.md`. |
| **Environments** | `dev` / `prod` (hub), `apidev` / `dev` / `gitopsdev` / `gitopsprod` (spokes), `dev` (atmsec, gai). |
| **Hub state subscription** | `cc31f800-80a8-4e8b-9cf5-a20a232a5b39` — where terraform backend state lives. |
| **Tenant** | `da280eb2-7328-4fc8-9521-154de96d70eb`. |

## Key concepts explained

### The DCV state machine

Each domain being managed by the hub is in one of these `DomainState` values (from `domain_state.py`):

- `NOT_EXISTS` — DigiCert has no record of the domain.
- `EXISTS_INACTIVE` — DigiCert has the domain but it's not active.
- `EXISTS_ACTIVE_NOT_VALIDATED` — active but no DCV ever submitted.
- `EXISTS_ACTIVE_PENDING_VALIDATION` — DCV submitted, awaiting DigiCert check.
- `EXISTS_ACTIVE_VALIDATED_OK` — validated and not near expiry.
- `EXISTS_ACTIVE_VALIDATED_EXPIRING` — validated but within the expiry threshold.

…and the Watchdog chooses one `DomainAction`:

- `NO_ACTION`
- `ADD_DOMAIN` (to DigiCert)
- `ACTIVATE_DOMAIN`
- `SUBMIT_FOR_VALIDATION` (writes the `_dnsauth` TXT via DME)
- `RESUBMIT_PENDING_TIMEOUT` (pending > timeout hours)
- `CLEANUP_DCV_RECORDS` (TXT older than cleanup period)

Each action is a dedicated module in `actions/` and receives a small struct of clients (DigiCert / DME / KeyVault) + the domain record.

### Why hub/spoke?

- Application workloads live in many subscriptions. Giving each workload direct DigiCert access would multiply API keys and audit surface.
- Instead, one hub owns DigiCert + DME. Spokes hold only a local copy of the cert that their workloads need.
- Event Grid is the one-way push channel hub → spoke; FullSync is the safety net.
- Adding a new spoke is a pipeline-driven operation documented in `domain-management/docs/runbooks/adding-spoke-for-cert-distribution.md`.

### Why `atmsec` is separate

- DigiCert + DME credentials are org-wide, not environment-specific, and are rotated on their own cadence by security.
- Keeping them in a dedicated GA stack means the hub/spoke Incubating stacks can be rebuilt without touching the source of truth for credentials.
- The `lifecycle { ignore_changes = [value] }` on the secret values lets terraform own the *shape* (name, tags, RBAC) but not the *value*.

### Why DNS Made Easy?

- Milliman LTS's DNS authoritative provider. DigiCert's DCV-by-DNS works against whatever authoritative DNS exists — the watchdog uses the DME provider + API to keep the record lifecycle entirely automated.

### Why Flex Consumption (FC1)?

- Scale-to-zero Python functions. The Watchdog runs once a day; spoke CertSyncs run only when certs move. Flex Consumption avoids paying for idle App Service Plans while still giving VNet-friendly features where needed.

### What "Incubating" vs "Generally Available" means here

- Stated in the stack's `readme.md`. Incubating = interfaces and inputs may change; treat each new env rollout as a design review.
- `atmsec` is GA because it's very small and stable. The hub/spoke/GAI stacks are still evolving.

## Naming conventions

| Thing | Convention | Example |
|---|---|---|
| Hub KV | `sslcerthub-<env>` | `sslcerthub-dev` |
| Event Grid system topic | `hub-kv-cert-events-<env>` | `hub-kv-cert-events-dev` |
| Spoke queue | `cert-sync-events` | (same per spoke) |
| DCV token secret | `<domain-with-dots-as-dashes>-dcv-token` | `api-example-com-dcv-token` |
| DCV timestamp secret | `<domain-with-dots-as-dashes>-dcv-requested-at` | `api-example-com-dcv-requested-at` |
| Resource group | Set per env in `configuration/<env>.json` | — |
| Make target (stack) | `<prefix>__<action>` | `domain__infra__plan` |

## Environments

| Env | In stacks | Purpose |
|---|---|---|
| `dev` | atmsec, domain-management, ssl-cert-spoke, global-app-ingress | Shared dev environment. |
| `prod` | domain-management | Production hub. |
| `apidev` | ssl-cert-spoke | API product's dev spoke. |
| `gitopsdev` | ssl-cert-spoke | GitOps dev spoke. |
| `gitopsprod` | ssl-cert-spoke | GitOps prod spoke. |
