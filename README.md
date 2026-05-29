# zzang-codex-skills

Codex CLI custom skills by kimseungzzang — session context shared with [zzang-claude-skills](https://github.com/Kimseungzzang/zzang-claude-skills).

## Install

```bash
npx zzang-codex-skills
```

Local development install:

```bash
node bin/install.js
```

The installer automatically:
- Installs all skills into `~/.codex/skills/`
- Installs shared scripts into `~/.zzang/scripts/`
- Registers `PreToolUse`, `PostToolUse`, `Stop`, and `PreCompact` hooks in `~/.codex/hooks.json`
- Guides you through setting up a private GitHub repo for session storage

**Restart Codex after install to activate hooks.**

## Skills

| Skill | Description |
|-------|-------------|
| `/session-save` | Compact the current session context and push to your sessions repo |
| `/session-load` | Pull the latest context for the **current project** and resume where you left off |
| `/obsidian` | Summarize today's conversation and save it to your Obsidian vault |
| `/github-summary` | Fetch a GitHub repo URL and summarize it |
| `/git-conventions` | Guide commit message writing using Conventional Commits |
| `/codex-review-loop` | Run Codex CLI review, fix issues, repeat until clean |
| `/spring-dev` | Apply Spring Boot coding and review rules |
| `/spring-dev-loop` | Review, test, and fix Spring Boot changes until clean |

## Session Sharing with Claude Code

Both `zzang-claude-skills` and `zzang-codex-skills` read/write the same `~/.zzang/ctx/` directory and the same GitHub sessions repo.

```
Claude Code session → /session-save → CURRENT.ctx → git push → GitHub
                                                                    ↓
                                                               git pull
                                                                    ↓
Codex session      → /session-load → CURRENT.ctx → resume where you left off
```

Switch freely between Claude Code and Codex — context follows you.

## Scripts

Scripts install to `~/.zzang/scripts/` and are shared between both tools:

| Script | Hook | Purpose |
|--------|------|---------|
| `git-conventions-check.sh` | PreToolUse | Enforce Conventional Commits for `git commit -m` |
| `task-log.sh` | PostToolUse | Log every tool use to `task-log.md`; accepts both Claude Code and Codex hook payload shapes without `jq` |
| `dragon-notify.sh` | Stop | Notify claude-dragon desktop companion |
| `pre-compact-backup.sh` | PreCompact | Absorb task-log into CURRENT.ctx before compaction |
