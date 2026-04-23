# Checkov Plan Scanning

- **Category:** dev
- **Status:** active
- **Owners:** Platform Engineering

## Summary

The package stage runs Checkov (major version 3) against the Terraform plan and publishes the results as a JUnit report so they surface on the Azure DevOps Tests tab. Scans are informational today — findings do not fail the build — but known findings can be suppressed inline with `#checkov:skip=` comments.

## Behavior

- Checkov executes via the `checkov` compose service: `integratesolutions.azurecr.io/integrate/tooling/checkov:3` (`docker-compose.yaml:34`).
- The package make target (in `cicd-shared/make/terraform.mk`) emits `checkov-results.xml` into the stack directory.
- `shared/azdo-templates/package-iac-template.yaml:40` publishes that file via `PublishTestResults@2` with title `<artifact_prefix> - Checkov Scan results`.
- `.gitignore` excludes `checkov-results.xml` from the working tree (`.gitignore:20`).
- Inline suppressions today: `CKV_AZURE_107` and `CKV_AZURE_174` on the APIM resource (`ingress/deploy/modules/regional_apim/apim.tf:10`).

## Entry Points

- `docker-compose.yaml:34` — Checkov service.
- `shared/azdo-templates/package-iac-template.yaml:40` — `PublishTestResults@2` step.
- `ingress/deploy/modules/regional_apim/apim.tf:10` — example suppression comments.
- `.gitignore:20` — results file is gitignored.

## Key Dependencies

- Checkov container from the internal ACR.
- [Reusable ADO IaC Templates](reusable-azdo-templates.md) — host of the publish step.
- [Containerised Toolchain](containerised-toolchain.md).

## Related Features

- [Reusable ADO IaC Templates](reusable-azdo-templates.md)
- [Conftest Policy Harness](conftest-policy.md)

## Change Log

- 2026-04-21: Seeded.
