# Software KMS

Software Knowledge Management System for the `milliman-lts` GitHub organization.

An OpenCode-driven workspace that seeds thorough documentation for repos, ingests merged PRs daily to keep docs current, and exposes a dedicated agent for domain Q&A.

## Prerequisites

- [OpenCode](https://opencode.ai) installed and running in this directory.
- [GitHub CLI (`gh`)](https://cli.github.com/) installed and authenticated with read access to `milliman-lts`.
- `git` installed.
- Directory `C:/Projects/software-kms-repos/` will be created on first `/seed` for local clones.

Verify auth:

```sh
gh auth status
gh repo list milliman-lts --limit 1
```

## Layout

```
software-kms/
├── .opencode/
│   ├── agent/kms.md                   # KMS Agent (read-only Q&A)
│   └── command/
│       ├── seed.md                    # /seed <repo-name>
│       ├── pull.md                    # /pull
│       └── push.md                    # /push
└── kms/
    ├── .state.json                    # per-repo seed/pull tracking
    ├── _pulls/YYYY-MM-DD.md           # daily PR digests
    └── <repo-name>/
        ├── overview.md
        ├── tech.md
        ├── architecture.md
        ├── domain.md
        ├── notes.md                   # append-only changelog
        └── features/
            ├── _index.md              # catalog (business + dev)
            ├── business/<slug>.md
            └── dev/<slug>.md
```

## Commands

### `/seed <repo-name>`

Reads a `milliman-lts` repo exhaustively and generates a full knowledge library under `kms/<repo-name>/`.

- Clones (or refreshes) the repo at `C:/Projects/software-kms-repos/<repo-name>/`.
- Writes `overview.md`, `tech.md`, `architecture.md`, `domain.md`, `features/_index.md`, and per-feature files under `features/business/` and `features/dev/`.
- Records seed state in `kms/.state.json`.
- **Writes files only. Does not stage, commit, or push.**

### `/pull`

Reads all PRs merged across seeded repos since each repo's `lastPulledAt`, updates the knowledge library, and produces a digest.

- Fetches latest default branch in each local clone.
- Updates affected feature files and appends to `notes.md`.
- Writes digest to `kms/_pulls/YYYY-MM-DD.md` (appends a timestamped section on same-day re-runs).
- Updates `kms/.state.json`.
- **Writes files only. Does not stage, commit, or push.**

### `/push`

Stages everything (`git add -A`), commits with a sensible default message (or one you provide), and pushes to `main`.

- Refuses to push from a non-`main` branch or with merge conflicts.
- Never force-pushes.

## Agents

### KMS Agent (`kms`)

Tab-select the `kms` agent to ask questions about the knowledge base.

- Read-only: `read`, `glob`, `grep` only.
- Default scope: `kms/**`.
- May escalate to local clones at `C:/Projects/software-kms-repos/<repo>/` if `kms/` is insufficient, and will say so.
- Cites `path:line` references.

## Typical Workflow

```
/seed some-repo          # one-time per repo
# ...next day...
/pull                    # ingest yesterday's merged PRs
# skim kms/_pulls/YYYY-MM-DD.md
/push                    # publish KMS updates
```

Ask the KMS agent things like:
- "What does `some-repo` do?"
- "What features shipped in `some-repo` this week?"
- "Which repo owns pricing calculations?"
