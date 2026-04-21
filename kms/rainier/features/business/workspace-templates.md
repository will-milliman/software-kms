# Workspace Templates

- **Category:** business
- **Status:** active
- **Owners:** authoring-and-governance

## Summary

Named, governed bundles of projection configuration that can be imported into projects and listed/edited. Lets standardized projection setups be reused across projects under a single branch context.

## Behavior

- Each project-branch has a workspaces surface: list templates, read a template, update, and import a template (applying its config).
- V2 of the templates endpoint returns extended metadata.

## Entry Points

- `src/server/Rainier.Governance/Rainier.Governance.Server/Controllers/WorkspacesController.cs:16` — base `api/projects/{projectId}/branches/{branchId}/workspaces`:
  - `:28` `PUT config/import`
  - `:42` `PUT config/import/{templateId}`
  - `:56` `GET templates`
  - `:68` `GET templates/v2`
  - `:80` `GET config/{templateId}`
  - `:97` `PUT config/{templateId}`

## Key Dependencies

- [Governance](governance.md) — hosting service.
- MediatR command handlers.

## Related Features

- [Governance](governance.md)
- [Project Authoring](project-authoring.md)

## Change Log

- 2026-04-21: Seeded.
