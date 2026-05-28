# zzang-codex-skills

Codex CLI custom skills by kimseungzzang — session context shared with [zzang-claude-skills](https://github.com/Kimseungzzang/kimseungzzang-claude-skills).

## Install

```bash
node bin/install.js
```

The installer automatically:
- Installs all skills into `~/.codex/skills/`
- Installs shared scripts into `~/.zzang/scripts/`
- Registers `PostToolUse`, `Stop`, `PreCompact` hooks in `~/.codex/hooks.json`
- Guides you through setting up a private GitHub repo for session storage

**Restart Codex after install to activate hooks.**

## Skills

| Skill | Description |
|-------|-------------|
| `/session-save` | Compact the current session context and push to your sessions repo |
| `/session-load` | Pull the latest context for the **current project** and resume where you left off |
| `/obsidian` | Summarize today's conversation and save it to your Obsidian vault |
| `/github-summary` | Fetch a GitHub repo URL and summarize it |

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

## Scripts (shared with zzang-claude-skills)

Scripts install to `~/.zzang/scripts/` and are shared between both tools:

| Script | Hook | Purpose |
|--------|------|---------|
| `task-log.sh` | PostToolUse | Log every tool use to `task-log.md`; accepts both Claude Code and Codex hook payload shapes without `jq` |
| `dragon-notify.sh` | Stop | Notify claude-dragon desktop companion |
| `pre-compact-backup.sh` | PreCompact | Absorb task-log into CURRENT.ctx before compaction |
