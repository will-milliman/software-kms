# Networking Spike (Hub-and-Spoke VNet Topology)

- **Category:** business
- **Status:** active (Spike — incubating)
- **Owners:** Platform Engineering (LTS)

## Summary

A standalone Terraform spike (`spike-networking/`) that proves out a hub-and-spoke Azure virtual-network topology spanning multiple subscriptions for the **API Ingress** initiative (AB#90473, AB#90486). It introduces reusable shared modules — `network`, `network-topology`, `hub-vnet`, `spoke-vnet`, `vnet-peering`, `private-dns`, `subscriptions` — that wire a hub VNet (intended for AFD/APIM ingress) to per-foundation spoke VNets (api-foundations, connectivity-foundations, global-services) with VNet peering and shared private DNS zones.

## Behavior

- Deploys as its own stack from `spike-networking/deploy/` with its own pipeline (`spike-networking/.pipelines/infra-cd.yaml`) and helper scripts (`plan-spike.sh`, `validate-spike.sh`, root `make.mk`).
- The `network-topology` shared module is the orchestrator: it instantiates a hub VNet plus N spoke VNets, peers each spoke to the hub, and links them to the shared private DNS zones.
- Each foundation's subscription is targeted from `spike-networking/configuration/<foundation>-dev.json` plus a static `subscription-map.json` in `shared/modules/subscriptions/`.
- Private DNS zones are provisioned in the hub and linked into spokes via the `network` and `private-dns` modules so workloads resolve internal service hostnames consistently across the topology.
- State backend is configured via `spike-networking/configuration/backend.hcl`.

## Entry Points

- Spike stack root: `spike-networking/deploy/main.tf`, `provider.tf`, `outputs.tf`, `variables.tf`, `tags.tf`, `make.mk`.
- Spike pipeline: `spike-networking/.pipelines/infra-cd.yaml`.
- Per-foundation config: `spike-networking/configuration/{api-foundations,connectivity-foundations,global-services}-dev.json`.
- Backend config: `spike-networking/configuration/backend.hcl`.
- Shared modules added by the spike:
  - `shared/modules/network/` — top-level network composition (VNet + private DNS zone wiring).
  - `shared/modules/network-topology/` — orchestrator: hub + spokes + peering + DNS links.
  - `shared/modules/hub-vnet/` — hub VNet primitive.
  - `shared/modules/spoke-vnet/` — spoke VNet primitive (with private DNS zone link inputs).
  - `shared/modules/vnet-peering/` — bidirectional VNet peering primitive.
  - `shared/modules/private-dns/` — private DNS zone + VNet links.
  - `shared/modules/subscriptions/` + `subscription-map.json` — subscription lookup.
- Helper scripts: `plan-spike.sh`, `validate-spike.sh` (root); `Makefile` exposes spike targets.
- README: `spike-networking/readme.md`.

## Key Dependencies

- Azure provider (Terraform `>= 1.14.5` per repo standard).
- [Terraform Four-Stack Layout](../dev/terraform-four-stack-layout.md) — spike adds a fifth (incubating) stack.
- [Three Musketeers Build](../dev/three-musketeers-build.md).
- [Azure DevOps OIDC Pipelines](../dev/azure-devops-oidc-pipelines.md).

## Related Features

- [Global App Ingress (Azure Front Door)](global-app-ingress-frontdoor.md) — downstream consumer once the spike graduates.

## Change Log

- 2026-04-23: PR #36 Networking spike (AB#90473) — initial spike: `spike-networking/` stack + new shared modules `network`, `network-topology`, `hub-vnet`, `spoke-vnet`, `vnet-peering`, `subscriptions`; helper scripts and per-foundation configs.
- 2026-04-23: PR #37 chore(AB#90472): setting the spokes to be deployed — flipped `network-topology/main.tf` to actually deploy the spoke VNets.
- 2026-04-23: PR #38 feat(AB#90473): adding private dns zones to the spike — added `shared/modules/private-dns/` and wired private DNS zones through `network/main.tf` and `spoke-vnet/main.tf`; pipeline updated.
