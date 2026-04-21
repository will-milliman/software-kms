# rainier

- **Repo:** [milliman-lts/rainier](https://github.com/milliman-lts/rainier)
- **Default branch:** `main`
- **Seed date:** 2026-04-21

## Purpose

`rainier` is the primary mono repo for the **Milliman Integrate** application — the desktop platform actuaries use to author, govern, run, and release MG-ALFA actuarial models. It consolidates the Integrate Electron desktop app, the legacy MG-ALFA "Classic" model editor, the Model Development (MMD) collaborative server + desktop, projection execution services, and numerous supporting backends and libraries.

## Target audience

- **Business users:** Actuaries and modelers authoring MG-ALFA models (projections, ETLs, tables, databases, inforce), running them locally or on grid/cloud compute, and releasing them to IFP/IDM environments.
- **Internal developers:** The entire LTS engineering org (authoring & governance, frontend platform, projection execution, calculation & execution, documentation, SDK teams).

## High-level capabilities

- **Integrate Desktop App** — Electron shell + React/Vite SPA + multiple .NET server processes (Project Manager, Run Manager, Governance, Application Host, ACL).
- **MG-ALFA Classic Editor** — WinForms/WPF + Dyalog APL desktop editor for Ain2/Adb2/Atb2 model files, formulas, and tables.
- **Projection Execution** — Orchestrates local, grid, and cloud runs of the native calc engine (AHost) against authored models.
- **Model Development (MMD)** — Developer Desktop (WPF) + Developer Site (ASP.NET + workers) for collaborative model authoring with three-way diff / conflict resolution.
- **ACL service** — Resource-based access control for Integrate projects and artifacts.
- **Origin** — Compute-side SAS/URI handout for cloud jobs.
- **Extensive CI** — Azure DevOps pipelines, Playwright E2E orchestration, cross-repo Makefile utilities, AI agent definitions, prompts, skills, ADRs.

## Key tech

C#/.NET 8, ASP.NET Core, C++, Dyalog APL, TypeScript, React 19, Vite, Electron 34, pnpm, Redux Toolkit, SignalR, MediatR, Carbon Design System, Docker, Azure DevOps Pipelines, GitHub Actions.

## Dominant languages (by repo size)

C (44 MB), C# (33 MB), Standard ML (6 MB), TypeScript (4.8 MB), C++ (1.2 MB), PowerShell (249 KB), JavaScript (230 KB), HTML (303 KB), Python (166 KB), CoffeeScript (112 KB), Shell (102 KB), Vue (69 KB), Makefile (48 KB).

## Topics

`asp-net-core`, `authoring-and-governance`, `desktop`, `integrate-app`, `knockoutjs`, `mgalfa-classic`, `mmd`, `razor`, `react`, `vsto`, `winforms`, `wpf`, `electron`, `milliman-design-system`, `storybook`, `typescript`.
