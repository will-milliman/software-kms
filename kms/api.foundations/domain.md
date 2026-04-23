# Domain

## Glossary

- **API Foundation** — the shared platform capability this repo exists to provide: a standardized, centrally-owned entry point for LTS APIs (DNS + WAF + Front Door + APIM).
- **Ingress stack** — the single Terraform component under `ingress/` that materializes the API Foundation in Azure.
- **AFD / Azure Front Door** — the global L7 front end (Premium SKU) used for TLS termination, routing, and WAF enforcement.
- **Origin group / origin** — AFD constructs. The stack has one origin group (`api-og`) containing one origin per APIM region.
- **APIM / Azure API Management** — the per-region API gateway tier that sits behind Front Door. Currently provisioned at the `Developer_1` SKU (non-HA; for lower environments or pre-production use).
- **Regional APIM module** — the Terraform child module at `ingress/deploy/modules/regional_apim/` that encapsulates one APIM instance + managed identity + AFD origin + diagnostics.
- **WAF policy** — the `azurerm_cdn_frontdoor_firewall_policy` resource named `globalapiwafpolicy`. Runs managed rule sets in **Detection/Log** mode plus two **Block** custom rules (TRACE method, sanctioned geos).
- **Security policy** — the AFD object binding the WAF policy to the custom domain for `/*`.
- **DME / DNS Made Easy** — the external DNS provider used for authoritative zones (`plt.integrate-solutions.com` today).
- **Integrate workspace** — the Log Analytics workspace `integrate-monitoring-us` used for operational telemetry.
- **Sentinel workspace** — the LTS Sentinel Log Analytics workspace `ltssentinelsecurity` used for security signal.
- **`connsec-<env>` Key Vault** — stores DME API credentials consumed by the make layer at plan/apply time. `plt` reuses the `dev` moniker (`ingress/deploy/make.mk:17`).
- **`env-local.mk`** — untracked developer file (template `env-local.mk.tpl`) that sets `ENVIRONMENT`, `SKIP_DME_SECRETS`, `USE_COMPOSE` for local runs.
- **`cicd-shared`** — git submodule at `./cicd-shared` providing shared make helpers (`help.mk`, `terraform.mk`). Source: `https://github.com/milliman-lts/cicd-shared.git`, pinned to branch `main`.
- **Lifecycle: Incubating** — the repo's documented maturity stage; see [life-cycle-designations.md](https://github.com/milliman-lts/engineering.foundations.github/blob/main/docs/life-cycle-designations.md).

## Core entities & relationships

```
ResourceGroup (local.resource_identifier)
  ├── FrontDoorProfile (Premium)
  │     ├── Endpoint  ── route /* ──┐
  │     ├── OriginGroup api-og <────┤
  │     ├── CustomDomain api.<base> ─── Secret (KV cert) ── SecurityPolicy ── WAFPolicy
  │     └── DiagnosticSettings (Integrate LA + Sentinel)
  └── module.regional_apim[<region>]  (one per var.apim_regions)
        ├── UserAssignedIdentity  apim-<region>-<env>-identity
        ├── APIM Developer_1      apim-<region>-<env>
        ├── AFD Origin            apim-<region>-<env> -> origin group api-og
        └── DiagnosticSetting     (Integrate LA)
DNS Made Easy domain (var.base_domain_name)
  └── CNAME  api.<base_domain_name> -> AFD endpoint hostname
```

## Environments

Defined by `ingress/configuration/<env>.json`:

| Env | File | Subscription | Regions | Base domain | Cert in KV? |
| --- | --- | --- | --- | --- | --- |
| `dev` | `ingress/configuration/dev.json` | `dc0e9c01-02e7-4ccc-ae9f-6a3512693c23` | eastus, westus | `plt.integrate-solutions.com` | Yes — `sslcertspokegitopsdev`, cert `preview-integrate-solutions-com` |
| `plt` | `ingress/configuration/plt.json` | `dc0e9c01-02e7-4ccc-ae9f-6a3512693c23` | eastus, westus | `plt.integrate-solutions.com` | **Empty string** (must be populated before first apply) |

Both environments share the `integrate-gtm` resource identifier, so the resource group becomes `integrate-gtm-dev` / `integrate-gtm-plt`.

## Business rules encoded in code

1. **WAF is in Detection, not Prevention.** Managed rule sets have `action = "Log"` (`ingress/deploy/afd_waf.tf:14`, `:20`). Custom rules are the only enforcement points today.
2. **`TRACE` is always blocked**, priority 110 (`ingress/deploy/afd_waf.tf:23`).
3. **Sanctioned geos are blocked** by socket address GeoMatch: BY, CN, CU, IR, KP, RU, SY, priority 10000 (`ingress/deploy/afd_waf.tf:37`).
4. **TLS is customer-managed.** AFD must use the certificate referenced by `base_domain_name_certificate_key_vault_id`; there is no fallback to a managed certificate (`ingress/deploy/afd_domain.tf:6`).
5. **HTTPS redirect is mandatory** for all routes (`ingress/deploy/afd_route.tf:11`), and `link_to_default_domain = false` ensures requests must come in on the custom domain.
6. **Environment → operational classification mapping:** `prod` maps to `production`, everything else uses the env name verbatim in the `environment-client-operational-classification` tag (`ingress/deploy/tags.tf:2`).
7. **Minimum APIM API version is `2019-12-01`** (`ingress/deploy/modules/regional_apim/apim.tf:26`).
8. **APIM public access is on at creation** with a comment noting it can be turned off post-create (`ingress/deploy/modules/regional_apim/apim.tf:20`). Also, two Checkov rules are explicitly skipped for APIM: CKV_AZURE_107 (VNet) and CKV_AZURE_174 (public access) (`ingress/deploy/modules/regional_apim/apim.tf:10`).
9. **Origin host fields must not contain scheme or trailing slash.** The module derives `apim_gateway_hostname` by trimming `https://` and `/` off `gateway_regional_url` (falling back to `gateway_url`) (`ingress/deploy/modules/regional_apim/afd_origin.tf:3`).
10. **State key convention:** `api.foundations/<stack>-<env>.tfstate`, so adding a new component stack must follow the `api.foundations/<stack>-*` naming (`ingress/deploy/make.mk:8`).
11. **CI path filters:** the `ingress` pipeline only triggers on changes under `ingress/**` or `docker-compose.yaml`, and explicitly ignores `ingress/README.md` and `ingress/docs/**` (`ingress/.pipelines/iac-deploy.yaml:6`, `:18`).
12. **Dev pipeline does not auto-deploy.** Only `tf_validate` and `dev_package` run; the deploy stage is commented out (`ingress/.pipelines/iac-deploy.yaml:97`).

## Open questions / authoring TODOs found in code

- `ingress/deploy/notes.md:1` — "Need to think about how we manage adding multiple routes."
- `ingress/deploy/afd.tf:27` — health probe for the origin group is commented out; the comment asks whether it is needed when only one instance per region is hosted.
- `ingress/deploy/afd_waf.tf:59` — "Need to add custom rules for allows Azure services/regions."
- `ingress/deploy/context.tf:4` — consider naming the WAF policy per environment/resource identifier rather than the hard-coded `globalapiwafpolicy`.
- `ingress/deploy/variables.tf:21` — "Should we use the API work to start separating out our workspaces?" (on `integrate_workspace_id`).
- `ingress/deploy/modules/regional_apim/apim.tf:26` — `min_api_version` comment asking "what are the options?"
- `ingress/deploy/tests/checks.rego` — the Conftest policy file is declared but empty.
