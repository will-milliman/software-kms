---
description: Stage all changes, commit with a sensible message, and push to main.
agent: build
---

# /push $ARGUMENTS

Stage all changes in the `software-kms` repo, commit, and push to `origin/main`.

`$ARGUMENTS` is an optional commit message. If provided, use it verbatim. If blank, propose a default (see below) and confirm with the user before committing.

## Hard Rules

- Only operate in the `software-kms` repo root (current working directory).
- **Refuse to push** if:
  - The current branch is not `main`. Report the branch and stop.
  - The working tree has unresolved merge conflicts. Report and stop.
- **Never** use `git push --force` or any destructive git flags.
- **Never** skip hooks (`--no-verify`).

## Steps

### 1. Inspect state

Run in parallel:

```sh
git status --porcelain=v1 -b
git diff --stat
```

If the branch line shows anything other than `## main...origin/main` (possibly ahead), STOP and tell the user what branch they're on.

If there are no changes at all, STOP and tell the user there is nothing to push.

Show the user the summary of what will be committed.

### 2. Determine commit message

If `$ARGUMENTS` is non-empty, use it as-is.

Otherwise, classify the pending changes:

- Only touches `kms/_pulls/<date>.md` (and supporting doc updates from a pull run) → `chore(kms): pull <date>`
- Only touches a new or substantially new `kms/<repo>/` tree → `chore(kms): seed <repo>`
- Only touches `.opencode/` → `chore(opencode): update commands/agents`
- Mixed or other → `chore(kms): update YYYY-MM-DD`

Ask the user to confirm the proposed message before proceeding.

### 3. Stage, commit, push

```sh
git add -A
git commit -m "<message>"
git push origin main
```

### 4. Report

Output:

- The commit SHA (`git rev-parse HEAD`).
- The remote URL (`git config --get remote.origin.url`).
- Confirmation that the push succeeded.

## Reminders

- Never force-push.
- Never push from a non-`main` branch.
- If a pre-commit hook modifies files and the commit succeeds, leave the result as-is; do not amend unless the user explicitly asks.
- If the commit fails, fix the issue and create a new commit — never amend a failed commit.
