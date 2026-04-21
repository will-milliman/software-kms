# Ring-Based Rollout (Business)

- **Category:** business
- **Status:** active
- **Owners:** @milliman-lts/platform-engineering

## Summary

Customers are assigned to **rings** (`stable` by default, plus beta/preview tiers) via their group memberships in each environment. The ring they land on determines which software build, config set, and release notes they see — allowing staged rollout of changes without per-tenant work.

## Behavior

- Each environment maps **group → ring** via `EnvironmentModel.RingMap`; tenant admins set this via `POST UpdateGroupRing` (see [Environment & Ring Rollout](./environment-ring-rollout.md)).
- The Issuer puts a `ring` claim (`http://schemas.milliman.com/identity/claims/ring`) on every token issued.
- Downstream Config and Update services partition their responses by `UpdateRingKey` (tenant + environment + ring), so different rings see different payloads.
- Default ring is `"stable"` (`auth-server/src/Integrate.SaaS.AuthServer/Constants.cs:21`).

## Entry Points

- `tenant-admin/src/Integrate.Gateway.TenantManager.Api/Controllers/EnvironmentsController.cs:91` — `UpdateGroupRing`.
- `auth-server/src/Integrate.SaaS.AuthServer/Constants.cs:21` — `DefaultUpdateRing`.
- `auth-server/src/Integrate.SaaS.AppServices.ConfigurationService/Models/UpdateRingKey.cs` — partition key.

## Key Dependencies

- `ring` JWT claim.
- ConfigurationService + UpdateService ring partitioning.

## Related Features

- [Ring-Based Rollout (Dev)](../dev/ring-based-rollout.md)
- [Environment & Ring Rollout](./environment-ring-rollout.md)
- [Tenant Configuration Service](./tenant-configuration-service.md)
- [Software Update Distribution](./software-update-distribution.md)

## Change Log

- 2026-04-21: Seeded.
