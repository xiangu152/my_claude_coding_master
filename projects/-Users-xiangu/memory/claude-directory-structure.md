---
name: claude-directory-structure
description: Claude Code .claude 目录结构、文件用途、配置方式完整参考
metadata: 
  node_type: memory
  type: reference
  originSessionId: 983bfc71-4c2c-44cb-9f9c-3604eab321a9
---

# .claude 目录结构完整参考

> 来源: https://code.claude.com/docs/zh-CN/claude-directory

## 核心概念

Claude Code 从两个位置读取配置：
1. **项目目录** `.claude/` — 提交到 git 与团队共享
2. **全局目录** `~/.claude/` — 个人配置，适用于所有项目

Windows 上 `~/.claude` 解析为 `%USERPROFILE%\.claude`。可通过 `CLAUDE_CONFIG_DIR` 环境变量自定义。

## 项目级文件（仓库中）

| 文件 | 范围 | 提交 | 作用 |
|------|------|------|------|
| `CLAUDE.md` | 项目 | ✓ | 每个会话加载的指令（约定、命令、架构上下文） |
| `.claude/settings.json` | 项目 | ✓ | 权限、hooks、环境变量、模型默认值 |
| `.claude/settings.local.json` | 项目 | ✗ | 个人覆盖，自动 gitignored |
| `.claude/rules/*.md` | 项目 | ✓ | 主题范围的指令，可选路径门控 |
| `.claude/skills/<name>/SKILL.md` | 项目 | ✓ | 可重用提示，用 `/name` 调用或自动调用 |
| `.claude/commands/*.md` | 项目 | ✓ | 单文件提示，与 skills 相同机制 |
| `.claude/agents/*.md` | 项目 | ✓ | Subagent 定义（自己的提示和工具） |
| `.claude/workflows/*.js` | 项目 | ✓ | 动态工作流脚本，每个文件成为 `/<name>` 命令 |
| `.claude/agent-memory/<name>/` | 项目 | ✓ | Subagents 的持久内存 |
| `.claude/output-styles/*.md` | 项目 | ✓ | 自定义系统提示部分 |
| `.mcp.json` | 项目 | ✓ | 团队共享的 MCP 服务器 |
| `.worktreeinclude` | 项目 | ✓ | Gitignored 文件复制到新 worktrees |

## 全局级文件（~/）

| 文件 | 作用 |
|------|------|
| `~/.claude/CLAUDE.md` | 全局指令，适用于所有项目 |
| `~/.claude/settings.json` | 全局权限、hooks、环境变量 |
| `~/.claude/rules/*.md` | 全局主题范围指令 |
| `~/.claude/skills/<name>/SKILL.md` | 全局可重用提示 |
| `~/.claude/commands/*.md` | 全局单文件提示 |
| `~/.claude/agents/*.md` | 全局 subagent 定义 |
| `~/.claude/workflows/*.js` | 全局工作流脚本 |
| `~/.claude.json` | 应用状态、OAuth、UI 切换、个人 MCP 服务器 |

## 文件选择指南

| 想要 | 编辑 | 范围 |
|------|------|------|
| 为 Claude 提供项目上下文和约定 | CLAUDE.md | 项目或全局 |
| 允许/阻止特定工具调用 | settings.json permissions 或 hooks | 项目或全局 |
| 在工具调用前后运行脚本 | settings.json hooks | 项目或全局 |
| 设置环境变量 | settings.json env | 项目或全局 |
| 个人覆盖（不提交 git） | settings.local.json | 仅项目 |
| 添加 `/name` 调用的技能 | skills/<name>/SKILL.md | 项目或全局 |
| 定义专门的 subagent | agents/*.md | 项目或全局 |
| 编排多个 subagent | workflows/*.js | 项目或全局 |
| 连接外部工具 | .mcp.json | 仅项目 |
| 更改响应格式 | output-styles/*.md | 项目或全局 |

## CLAUDE.md 最佳实践

- 目标 200 行以内，过长会降低遵从性
- 列出常用命令（build/test/format）
- 仅特定任务需要的内容移到 skill 或 path-scoped rule
- 也可放在 `.claude/CLAUDE.md` 保持项目根目录整洁
- 用 `/memory` 命令在会话中编辑

## 其他相关文件

| 文件 | 位置 | 用途 |
|------|------|------|
| `managed-settings.json` | 系统级别 | 企业强制设置，无法覆盖 |
| `CLAUDE.local.md` | 项目根目录 | 私人偏好，与 CLAUDE.md 一起加载，需手动 gitignore |
| 已安装 plugins | `~/.claude/plugins` | 克隆的市场、plugin 版本和数据 |

## 应用数据（~/.claude/ 下）

### 自动清理（默认 30 天）

| 路径 | 内容 |
|------|------|
| `projects/<project>/<session>.jsonl` | 完整对话记录 |
| `projects/<project>/<session>/subagents/` | Subagent 对话记录 |
| `projects/<project>/<session>/tool-results/` | 大型工具输出溢出 |
| `file-history/<session>/` | 编辑前快照（checkpoint 恢复） |
| `plans/` | Plan Mode 计划文件 |
| `debug/` | 调试日志（--debug 启动时） |
| `paste-cache/`、`image-cache/` | 大型粘贴和图像缓存 |
| `session-env/` | 每会话环境元数据 |
| `tasks/` | 任务列表 |
| `shell-snapshots/` | Shell 环境快照 |
| `backups/` | 配置迁移前的备份 |

### 保留直到手动删除

| 路径 | 内容 |
|------|------|
| `history.jsonl` | 所有输入的提示（向上箭头回忆） |
| `stats-cache.json` | 聚合令牌和成本计数 |
| `remote-settings.json` | 服务器管理设置的缓存 |

## 清除本地数据

```bash
# 预览删除计划
claude project purge ~/work/my-repo --dry-run

# 执行删除
claude project purge ~/work/my-repo

# 清除所有项目
claude project purge --all

# 脚本中使用
claude project purge ~/work/my-repo --yes
```

## 纯文本存储警告

记录和历史未加密，仅靠 OS 文件权限保护。减少暴露：
- 降低 `cleanupPeriodDays`
- 设置 `CLAUDE_CODE_SKIP_PROMPT_HISTORY` 环境变量
- 用权限规则拒绝读取凭证文件

## 设置优先级（从高到低）

1. 企业托管设置 (`managed-settings.json`)
2. CLI 标志 (`--permission-mode`, `--settings`)
3. 环境变量
4. 项目 settings.json
5. 全局 settings.json
