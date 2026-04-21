# rainier.releases — Overview

- **Repo:** [milliman-lts/rainier.releases](https://github.com/milliman-lts/rainier.releases)
- **Purpose:** Infrastructure-as-Code for the Rainier **Integrate app** release management process. Hosts the Azure DevOps YAML approval pipeline and helper scripts that gate, tag, and announce Integrate app release candidates.
- **Target audience:** Rainier release engineers, operations, QA, security, and the Authoring & Governance team who approve and publish Integrate releases.
- **Lifecycle designation:** Incubating. Custodian team: `@milliman-lts/authoring-and-governance`.
- **Description (GitHub):** "This repository contains the release pipeline for the Integrate app."
- **Topics:** `integrate-app`, `infrastructure-as-code-tooling`.

## High-level capabilities

- **Integrate App Approvals pipeline** (`.pipelines/integrate-release-approvals.yaml`) — multi-stage ADO pipeline triggered by upstream CI builds of Integrate app components (Electron client, GridUtils, Origin API, ACL, Initializers, Developer Site, Developer Desktop). It collects manual approvals from Dev, QA, Ops, and Security, then publishes release-body artifacts, tags the build as `ApprovedReleaseCandidate`, and notifies customers by email.
- **Release trigger helper** (`.pipelines/scripts/Invoke-IntegrateRelease.ps1`) — PowerShell script invoked by the downstream Classic Release pipeline that reads the approved release-body artifact, merges in release-specific fields, and triggers the Create-Release pipeline.
- **Client notification** (`.pipelines/scripts/ClientNotification/`) — Bash script that posts a release-announcement email via the SendGrid API, with audit-friendly, source-controlled recipient JSON files.

## Seed date

- 2026-04-21
