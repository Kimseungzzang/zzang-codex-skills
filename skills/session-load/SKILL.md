---
name: session-load
description: "Restore accumulated project context from CURRENT.ctx for the current project (shared with zzang-claude-skills)"
---

Restore accumulated project context from CURRENT.ctx for the current project.

## Step 1 — Ensure sessions repo is set up

```bash
git -C ~/.zzang/ctx rev-parse --git-dir 2>/dev/null
```

If NOT a git repo, check `~/.zzang/ctx-remote` for saved URL and clone.

## Step 2 — Pull latest

```bash
git -C ~/.zzang/ctx pull --rebase 2>/dev/null || true
```

## Step 3 — Find CURRENT.ctx for this project

```bash
git rev-parse --show-toplevel 2>/dev/null | xargs basename
cat ~/.zzang/ctx/$PROJECT/CURRENT.ctx 2>/dev/null
```

If file does not exist:
```
No saved context found for project: {PROJECT}
Run /session-save at the end of a session to start accumulating context.
```

## Step 4 — Compare task-log to determine state

```bash
SAVED_ID=$(grep "^TASK-LOG-ID:" ~/.zzang/ctx/$PROJECT/CURRENT.ctx | awk '{print $2}')
LAST_LOG=$(tail -1 ~/.zzang/ctx/$PROJECT/task-log.md 2>/dev/null | grep -o '^\[[0-9:]*\]' | tr -d '[]')
```

- **LAST_LOG == SAVED_ID**: clean state
- **LAST_LOG > SAVED_ID**: unsaved work exists — show entries, ask to resume
- **No task-log**: different machine — inform user

## Step 5 — Acknowledge and orient

```
📂 Context loaded for {project} (last saved: {SESSION timestamp})

Continuing: {1-sentence summary}

Key context:
• {CTX fact}

Pending TODOs: {TODO items}
Open questions: {OPEN items}
```

Keep total output under 150 words.
