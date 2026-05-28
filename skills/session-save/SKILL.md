---
name: session-save
description: "Compact the current conversation context and save per-project filtered snapshots to the sessions repo (shared with zzang-claude-skills)"
---

Compact the current conversation context and save per-project filtered snapshots to the sessions repo.

## Step 1 — Ensure sessions repo is set up

Check if ~/.claude/zzang-ctx is already a git repo:

```bash
git -C ~/.claude/zzang-ctx rev-parse --git-dir 2>/dev/null
```

**If it IS a git repo** → proceed to Step 2.

**If it is NOT a git repo**, check for a saved remote URL:

```bash
cat ~/.claude/zzang-ctx-remote 2>/dev/null
```

- **URL found** → clone it:
  ```bash
  git clone {saved_url} ~/.claude/zzang-ctx
  ```

- **No URL found** → ask the user:
  ```
  No sessions repo configured. Choose an option:

  1. I have an existing repo URL → paste it and I'll clone it
  2. Create a new private repo now → I'll guide you through it

  Which option? (1 or 2)
  ```

## Step 2 — Pull latest

```bash
git -C ~/.claude/zzang-ctx pull --rebase 2>/dev/null || true
```

## Step 3 — Detect projects worked on this session

```bash
git rev-parse --show-toplevel 2>/dev/null | xargs basename
```

Present the detected list and confirm with user.

## Step 4 — For each project: read task-log and existing CURRENT.ctx

```bash
cat ~/.claude/zzang-ctx/{project}/task-log.md 2>/dev/null
cat ~/.claude/zzang-ctx/{project}/CURRENT.ctx 2>/dev/null
```

## Step 5 — Write a filtered snapshot

File: `~/.claude/zzang-ctx/{project}/$(date '+%Y-%m-%dT%H-%M')`

```
SESSION {TIMESTAMP} | {/absolute/path/to/project} | {branch}
STACK: {lang/framework/db}
DONE: {items done this session}
CHANGED: {file(reason); file(new); file(del)}
TRIED: {what failed and why}
DECIDED: {decision — reasoning}
TODO: {pending tasks}
OPEN: {open questions}
CTX: {non-obvious facts}
```

## Step 6 — Merge into CURRENT.ctx

| Field | Rule |
|-------|------|
| `SESSION` | Always update to latest timestamp |
| `TASK-LOG-ID` | Replace with last task-log line timestamp |
| `STACK` | Union (deduplicate) |
| `DONE` | Replace with this session only |
| `CHANGED` | Replace with this session only |
| `TRIED` | Accumulate — never compress |
| `DECIDED` | Accumulate — never compress |
| `TODO` | Remove completed; add new |
| `OPEN` | Accumulate |
| `CTX` | Accumulate (union, deduplicate) |

## Step 7 — Clear task-log

```bash
rm ~/.claude/zzang-ctx/{project}/task-log.md 2>/dev/null || true
```

## Step 8 — Commit and push

```bash
git -C ~/.claude/zzang-ctx add .
git -C ~/.claude/zzang-ctx commit -m "session: {project-list} $(date '+%Y-%m-%dT%H:%M')"
git -C ~/.claude/zzang-ctx push
```

## Step 9 — Report

```
✅ Session saved for {N} project(s)
📤 Pushed to remote.
Run /session-load at the start of your next session.
```
