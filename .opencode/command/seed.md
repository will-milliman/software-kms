---
description: Exhaustively read a milliman-lts repo and generate a full KMS library under kms/<repo-name>/.
agent: build
---

# /seed $ARGUMENTS

Seed the Software KMS with exhaustive documentation for the repository **`$ARGUMENTS`** from the `milliman-lts` GitHub organization.

## Preconditions

- `gh` must be authenticated (`gh auth status`).
- `git` must be installed.
- `$ARGUMENTS` must be a non-empty repo name. If missing or blank, STOP and ask the user for the repo name.

## Hard Rules

- **Writes files only.** Do not run `git add`, `git commit`, `git push`, or any staging operation in the `software-kms` repo. Publishing is the job of `/push`.
- Do not modify `idea.md` or any file outside `kms/` and the local clone at `C:/Projects/software-kms-repos/$ARGUMENTS/`.
- Never commit or push inside the cloned repo either. Only fetch/checkout are allowed there.

## Steps

### 1. Verify the repo exists

Run:

```sh
gh repo view milliman-lts/$ARGUMENTS --json name,defaultBranchRef,description,url,languages,repositoryTopics
```

If this fails, STOP and report the error to the user. Do not proceed.

Capture:
- Default branch name (e.g., `main`).
- Description.
- Languages and topics.
- Repo URL.

### 2. Ensure a local clone exists at `C:/Projects/software-kms-repos/$ARGUMENTS/`

If the directory does not exist:

```sh
gh repo clone milliman-lts/$ARGUMENTS C:/Projects/software-kms-repos/$ARGUMENTS
```

If it already exists, refresh it (use the `workdir` parameter on Bash calls; never `cd`):

```sh
git fetch --all --prune
git checkout <defaultBranch>
git pull --ff-only
```

Capture the current HEAD SHA:

```sh
git rev-parse HEAD
```

### 3. Exhaustive read pass

Build a complete mental model of the repository.

- Walk the full directory tree (use `glob` with patterns like `**/*`).
- Read every source file, configuration, CI workflow, documentation page, and manifest.
- **Skip:** binaries, images, `node_modules/`, `bin/`, `obj/`, `dist/`, `.git/`, and the raw contents of lockfiles (referring to them by name is fine).
- For large repos, launch multiple `explore` subagents in parallel, each assigned a top-level subtree, to control context. Ask each to return structured notes: purpose, entry points, key files with `path:line`, and notable dependencies.

### 4. Synthesize the knowledge base

Create the following files under `kms/$ARGUMENTS/`. Create parent directories as needed.

#### `kms/$ARGUMENTS/overview.md`

- Repo name, purpose (1-2 sentences), target audience.
- Link to GitHub URL.
- High-level capabilities.
- Seed date.

#### `kms/$ARGUMENTS/tech.md`

- Languages, frameworks, runtimes, versions.
- Major dependencies and what they provide.
- Infrastructure: databases, queues, external services, deploy targets.
- Build tooling and CI (reference `.github/workflows/`, build scripts).

#### `kms/$ARGUMENTS/architecture.md`

- Component diagram in prose (or a mermaid block if useful).
- Entry points with `path:line` refs (main, controllers, handlers, CLI entrypoints).
- Data flow.
- Module/package layout.

#### `kms/$ARGUMENTS/domain.md`

- Glossary of domain terms used in the code.
- Core entities and their relationships.
- Business rules that are encoded in the code.

#### `kms/$ARGUMENTS/notes.md`

Create with a single line:

```
# Notes

Append-only change log maintained by `/pull`.
```

#### `kms/$ARGUMENTS/features/_index.md`

Catalog of features, grouped into **Business** and **Dev** sections. Each entry: name, link to the feature file, one-line summary. When a feature straddles both categories (e.g., feature flags), create **two separate files** (one under `business/`, one under `dev/`) and cross-link them in both `_index.md` rows.

#### `kms/$ARGUMENTS/features/business/<slug>.md` and `kms/$ARGUMENTS/features/dev/<slug>.md`

One file per feature. Use kebab-case slugs. Each file contains:

```markdown
# <Feature Title>

- **Category:** business | dev
- **Status:** active | deprecated | planned
- **Owners:** <if discoverable, else "unknown">

## Summary

<1-3 sentences>

## Behavior

<user-visible behavior for business features; developer-facing behavior for dev features>

## Entry Points

- `path/to/file.ext:LINE` — description

## Key Dependencies

- <library or internal module> — why it matters

## Related Features

- [<other feature>](../<category>/<slug>.md)

## Change Log

- YYYY-MM-DD: Seeded.
```

**Business features** = user/customer-visible capabilities.
**Dev features** = build, CI, tooling, internal developer experience.
**Straddling features** = separate files in both categories, cross-linked.

### 5. Update `kms/.state.json`

Read the existing file. Add or update the entry for `$ARGUMENTS`:

```json
{
  "org": "milliman-lts",
  "repos": {
    "$ARGUMENTS": {
      "seededAt": "<ISO timestamp now>",
      "defaultBranch": "<captured in step 1>",
      "lastPulledAt": "<ISO timestamp now>",
      "lastPulledSha": "<HEAD SHA from step 2>"
    }
  }
}
```

Preserve other repos' entries.

### 6. Report

Summarize to the user:

- Number of feature files created (business / dev counts).
- Files written under `kms/$ARGUMENTS/`.
- Reminder: run `/push` when ready to publish.

## Reminders

- No git staging, committing, or pushing. Ever. That's `/push`.
- If the repo is huge, prefer delegating subtree exploration to parallel `explore` subagents.
- Preserve existing entries in `kms/.state.json`.
