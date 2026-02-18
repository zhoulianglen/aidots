---
name: aidots
description: Scan, backup, restore and diff all your AI coding tool configurations
---

# aidots

Manage personalized configurations across all your AI coding tools. Supports Claude Code, Codex CLI, Cursor, Gemini CLI, Antigravity, GitHub Copilot, Windsurf, and Aider.

## Commands

The user may invoke this skill with any of the following:

- `/aidots` or `/aidots scan` — Scan for installed AI coding tools and list personalized config files
- `/aidots backup` — Back up all configs to a Git repository
- `/aidots diff` — Compare local configs against the backup
- `/aidots restore` — Restore configs from backup to local machine

## Behavior

### scan

Run the scan script and present results to the user:

```bash
bash "${CLAUDE_PLUGIN_ROOT}/scripts/scan.sh"
```

This detects installed AI coding tools, finds personalized config files, and shows a summary. Empty files, binary files, sensitive credentials, and system defaults are automatically excluded.

### backup

1. Check if `~/.aidots/config.json` exists with a `backup_dir` setting.
2. If not, ask the user to choose a backup directory. Suggest `~/dotai` as the default. Once chosen, save it to `~/.aidots/config.json`:
   ```json
   {
     "backup_dir": "~/dotai",
     "created_at": "2026-02-18T16:00:00Z"
   }
   ```
3. If the backup directory does not exist, create it and run `git init`.
4. Run the backup:
   ```bash
   bash "${CLAUDE_PLUGIN_ROOT}/scripts/backup.sh"
   ```
5. Report results to the user.

### diff

1. Verify `~/.aidots/config.json` exists. If not, tell the user to run `/aidots backup` first.
2. Run the diff:
   ```bash
   bash "${CLAUDE_PLUGIN_ROOT}/scripts/diff.sh"
   ```
3. Summarize changes: new files, modified files, deleted files.

### restore

1. Verify `~/.aidots/config.json` exists. If not, ask the user for the backup directory path.
2. Run the restore:
   ```bash
   bash "${CLAUDE_PLUGIN_ROOT}/scripts/restore.sh"
   ```
   For preview only:
   ```bash
   bash "${CLAUDE_PLUGIN_ROOT}/scripts/restore.sh" --dry-run
   ```
3. The script will prompt for confirmation per tool. Report results when done.

## Supported Tools

Detection is automatic based on `scripts/tools.conf`. Currently supported:

| Tool | Config Directory |
|------|-----------------|
| Claude Code | `~/.claude/` |
| Claude-Mem | `~/.claude-mem/` |
| Codex CLI | `~/.codex/` |
| Cursor | `~/.cursor/` |
| Gemini CLI | `~/.gemini/` |
| Antigravity | `~/.antigravity/` |
| GitHub Copilot | `~/.copilot/` |
| Windsurf | `~/.windsurf/` |
| Aider | `~/.aider/` |

Tools not installed on the user's machine are silently skipped.

## Adding New Tools

To add a new tool, edit `scripts/tools.conf`. Each line follows the format:

```
tool_id|Display Name|config_dir|include_globs|exclude_globs
```

Globs are comma-separated. Use `**` for recursive matching.
