# Power BI Reports

- **Category:** business
- **Status:** active
- **Owners:** UI Platform Team (sandbox)

## Summary

The Tenant Manager `/reports` route embeds a Power BI report using `powerbi-client-react`, demonstrating how Integrate surfaces business intelligence content inside the shell.

## Behavior

- Navigating to `/reports` inside `sandbox-tenant-manager` renders a `PowerBIEmbed` component with the configured report.
- The Power BI container sits inside the standard App Shell (Header, SideNav, ContentRegion).
- Authentication/config for the embed is passed via the component's configuration prop (per `powerbi-client-react` conventions).

## Entry Points

- `apps/web/sandbox-tenant-manager/src/routes/reports.tsx:4` — route.
- `apps/web/sandbox-tenant-manager/src/pages/reports/reports.page.tsx:4` — `ReportsPage` hosting `PowerBIEmbed`.

## Key Dependencies

- `powerbi-client-react@2.0.0` — React wrapper for Power BI embedding.
- `@integrate-core-ui/frame`, `themes` — shell + theming surrounding the embed.

## Related Features

- [Tenant Management](./tenant-management.md)
- [App Shell Frame](./app-shell-frame.md)

## Change Log

- 2026-04-21: Seeded.
