# app.foundations.trafficmanagement — Notes (append-only)

- 2026-04-21: Seeded.
- 2026-04-21: PR #30 fix(AB#91000): corrected typo in the key vault cert user role (kristens-work) — `domain-management/deploy/spoke_permissions.tf`.
- 2026-04-21: PR #24 Resolve issue with the terraform output.json file not being generated (JPScutt, AB#90486) — `global-app-ingress/deploy/outputs.tf` + `cicd-shared` submodule bump.
- 2026-04-21: PR #31 Extend the global ingress to grant the AFD identity permissions to the ssl kv (JPScutt, AB#90486) — `global-app-ingress/deploy/afd.tf`, `variables.tf`, `configuration/dev.json`.
- 2026-04-21: PR #32 Updated the cicd sub module version (JPScutt, AB#90486) — submodule pointer bump only, no tracked file changes.
- 2026-04-21: PR #33 Set missing env variable (JPScutt, AB#90486) — `global-app-ingress/deploy/make.mk`.
- 2026-04-22: PR #34 chore(AB#91000): adding the plt-integrate-solutions.com SSL cert to the api spoke keyvault (kristens-work) — `ssl-cert-spoke/configuration/apidev.json`.
- 2026-04-22: PR #35 Correct how the principal id is set for the role assignment (JPScutt, AB#90486) — `global-app-ingress/deploy/afd.tf`.
- 2026-04-23: PR #36 Networking spike (kristens-work, AB#90473) — new `spike-networking/` stack + shared modules `network`, `network-topology`, `hub-vnet`, `spoke-vnet`, `vnet-peering`, `subscriptions`; helper scripts (`plan-spike.sh`, `validate-spike.sh`).
- 2026-04-23: PR #37 chore(AB#90472): setting the spokes to be deployed (kristens-work) — `shared/modules/network-topology/main.tf` flipped to deploy spoke VNets.
- 2026-04-23: PR #38 feat(AB#90473): adding private dns zones to the spike (kristens-work) — added `shared/modules/private-dns/` and wired private DNS zones through `network/main.tf` and `spoke-vnet/main.tf`; pipeline updated.
