---
description: Ingest PRs merged since last pull across all seeded repos, update KMS docs, and emit a daily digest.
agent: build
---

# /pull

Read all PRs merged in seeded `milliman-lts` repos since each repo's `lastPulledAt`, update the knowledge base, and produce a daily digest.

## Preconditions

- `gh` authenticated.
- `git` installed.
- `kms/.state.json` exists and has at least one entry under `repos`. If empty, STOP and tell the user to run `/seed <repo-name>` first.

## Hard Rules

- **Writes files only.** No `git add`, no `git commit`, no `git push` in the `software-kms` repo. Publishing is `/push`'s job.
- Inside local clones at `C:/Projects/software-kms-repos/<repo>/`, only `git fetch`, `git checkout`, `git pull --ff-only`, `git show`, `git rev-parse`, and `gh pr ...` are allowed. Never commit or push there.
- Never delete KMS files. Mark removed features as `status: deprecated` or rename via `renamed-to:` instead.

## Steps

### 1. Load state

Read `kms/.state.json`. For each repo key under `repos`, note:

- `lastPulledAt`
- `defaultBranch`
- `lastPulledSha`

Capture the current run time as an ISO timestamp (`runAt`) and today's date (`YYYY-MM-DD`).

### 2. For each seeded repo

Use `workdir: C:/Projects/software-kms-repos/<repo>` for git commands.

#### 2a. Refresh the clone

```sh
git fetch --all --prune
git checkout <defaultBranch>
git pull --ff-only
```

If the local clone is missing, clone it first with:

```sh
gh repo clone milliman-lts/<repo> C:/Projects/software-kms-repos/<repo>
```

Capture the new HEAD SHA: `git rev-parse HEAD`.

#### 2b. List PRs merged since `lastPulledAt`

```sh
gh pr list --repo milliman-lts/<repo> --state merged --search "merged:>=<lastPulledAt>" --json number,title,body,mergedAt,author,url,mergeCommit,files --limit 200
```

Sort results by `mergedAt` ascending.

#### 2c. For each PR (oldest first)

Read the diff:

```sh
gh pr diff <number> --repo milliman-lts/<repo>
```

Or locally:

```sh
git show <mergeCommit.oid>
```

Decide which KMS files need changes:

- **Existing feature changed** → edit that feature file (`kms/<repo>/features/<category>/<slug>.md`). Append to its `## Change Log`: `- YYYY-MM-DD: PR #<n> <title> — <one-line what changed>`.
- **New feature** → create a new feature file using the template from `/seed`. Add a row to `kms/<repo>/features/_index.md` under the right category.
- **Name collision** — if a new feature has the same natural name as an existing one but is meaningfully different, extend the slug with clarifying word(s) (e.g., `reporting.md` vs `reporting-exports.md`). In **both** files, add a short note under the Summary that links to the other and explains the distinction.
- **Straddling feature** (business + dev concerns) → create separate files under `business/` and `dev/`, cross-link them, and add both rows to `_index.md`.
- **Cross-cutting changes** — update `tech.md`, `architecture.md`, or `domain.md` as appropriate (new framework, new service, new domain term, etc.).
- **Removed / renamed feature** — do not delete the file. Set `Status: deprecated` (or `renamed-to: <slug>`) and add a change log entry.

Append a dated bullet to `kms/<repo>/notes.md`:

```
- YYYY-MM-DD — PR #<n> [<title>](<url>) by <author>: <one-line summary> — affects: <kms paths updated>
```

#### 2d. Update `.state.json` for this repo

- `lastPulledAt` = `runAt`
- `lastPulledSha` = latest merged PR's merge commit SHA, or the current HEAD SHA if no PRs were merged
- Preserve `seededAt` and `defaultBranch`

### 3. Write the daily digest

Path: `kms/_pulls/YYYY-MM-DD.md`.

**If the file does not exist**, create it with:

```markdown
# Pull Digest — YYYY-MM-DD

## Run at HH:MM

<per-repo sections>
```

**If it already exists**, APPEND a new `## Run at HH:MM` section (never overwrite).

For each seeded repo, add a subsection:

```markdown
### <repo-name>

<if no PRs>
_No merged PRs since <lastPulledAt>._

<else, for each PR>
- **#<n>** [<title>](<url>) — <author> — merged <mergedAt>
  - Why it matters: <one-line>
  - Affected KMS paths: `kms/<repo>/...`, `kms/<repo>/...`
```

### 4. Save `kms/.state.json`

Write the updated state. Preserve all keys; only modify the repos whose state changed.

### 5. Report

Summarize to the user:

- Repos processed and PR counts.
- Path to the digest file.
- Reminder: run `/push` to publish.

## Reminders

- No git staging, committing, or pushing. Ever. That's `/push`.
- Always append to digests on same-day re-runs; never overwrite.
- Apply PR updates chronologically so change logs stay ordered.
- Use agent judgment on name collisions and straddling features; add clarifying notes in both files.
