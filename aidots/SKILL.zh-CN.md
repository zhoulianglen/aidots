---
name: aidots
description: 扫描、备份、恢复、对比所有 AI 编码工具的个性化配置
---

# aidots

管理所有 AI 编码工具的个性化配置。支持 Claude Code、Codex CLI、Cursor、Gemini CLI、Antigravity、GitHub Copilot、Windsurf 和 Aider。

## 命令

用户可以通过以下方式调用此技能：

- `/aidots` 或 `/aidots scan` — 扫描已安装的 AI 编码工具，列出个性化配置文件
- `/aidots backup` — 将所有配置备份到 Git 仓库
- `/aidots diff` — 对比本地配置与备份的差异
- `/aidots restore` — 从备份恢复配置到本机

## 行为说明

### scan（扫描）

运行扫描脚本并向用户展示结果：

```bash
bash "${CLAUDE_PLUGIN_ROOT}/scripts/scan.sh"
```

自动检测已安装的 AI 编码工具，找到个性化配置文件并展示汇总。空文件、二进制文件、敏感凭据和系统默认生成的内容会被自动排除。

### backup（备份）

1. 检查 `~/.aidots/config.json` 是否存在并包含 `backup_dir` 设置。
2. 如果不存在，询问用户选择备份目录。建议默认使用 `~/dotai`。确认后保存到 `~/.aidots/config.json`：
   ```json
   {
     "backup_dir": "~/dotai",
     "created_at": "2026-02-18T16:00:00Z"
   }
   ```
3. 如果备份目录不存在，创建并运行 `git init`。
4. 执行备份：
   ```bash
   bash "${CLAUDE_PLUGIN_ROOT}/scripts/backup.sh"
   ```
5. 向用户报告结果。

### diff（对比）

1. 确认 `~/.aidots/config.json` 存在。如果不存在，提示用户先执行 `/aidots backup`。
2. 执行对比：
   ```bash
   bash "${CLAUDE_PLUGIN_ROOT}/scripts/diff.sh"
   ```
3. 汇总变化：新增文件、修改文件、已删除文件。

### restore（恢复）

1. 确认 `~/.aidots/config.json` 存在。如果不存在，询问用户备份目录路径。
2. 执行恢复：
   ```bash
   bash "${CLAUDE_PLUGIN_ROOT}/scripts/restore.sh"
   ```
   仅预览不执行：
   ```bash
   bash "${CLAUDE_PLUGIN_ROOT}/scripts/restore.sh" --dry-run
   ```
3. 脚本会逐个工具请求确认。完成后报告结果。

## 支持的工具

基于 `scripts/tools.conf` 自动检测。当前支持：

| 工具 | 配置目录 |
|------|---------|
| Claude Code | `~/.claude/` |
| Claude-Mem | `~/.claude-mem/` |
| Codex CLI | `~/.codex/` |
| Cursor | `~/.cursor/` |
| Gemini CLI | `~/.gemini/` |
| Antigravity | `~/.antigravity/` |
| GitHub Copilot | `~/.copilot/` |
| Windsurf | `~/.windsurf/` |
| Aider | `~/.aider/` |

未安装的工具会被自动跳过。

## 添加新工具

编辑 `scripts/tools.conf`，每行格式：

```
工具ID|显示名称|配置目录|包含规则（glob）|排除规则（glob）
```

glob 规则用逗号分隔，`**` 表示递归匹配。
