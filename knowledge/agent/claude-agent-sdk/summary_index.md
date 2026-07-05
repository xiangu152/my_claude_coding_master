# Claude Agent SDK 知识库索引

> 版本：latest | 文档：https://code.claude.com/docs/en/agent-sdk/overview
> Claude Agent SDK 是构建 AI Agent 的 Python/TypeScript SDK，基于 Claude Code 的代理循环引擎。

---

## 核心架构

```
Claude Agent SDK 应用
├── 入口 (Entry Points)
│   ├── query() — 单次交互，返回消息流
│   ├── ClaudeSDKClient — 多轮连续对话
│   └── ClaudeAgentOptions — 配置选项 (model, tools, system_prompt, etc.)
│
├── Session (会话管理)
│   ├── session_id — 唯一标识
│   ├── continue_conversation — 继续对话
│   ├── resume — 恢复会话
│   └── fork — 分叉会话
│
├── Agent Loop (代理循环)
│   ├── 消息 → Claude 推理 → 工具调用 → 结果 → 循环
│   └── 流式输出 / 单次输出
│
├── Tools (工具)
│   ├── Custom Tools — Python/TypeScript 函数
│   ├── MCP Tools — Model Context Protocol
│   ├── Tool Search — 大规模工具管理
│   └── Built-in Tools — Read, Write, Edit, Bash, etc.
│
├── Subagents (子代理)
│   ├── 独立会话
│   ├── 可并行执行
│   └── 继承父代理上下文
│
├── Extensions (扩展)
│   ├── Skills — 可复用指令集
│   ├── Plugins — 功能扩展
│   ├── Slash Commands — 快捷命令
│   └── Hooks — 生命周期钩子
│
├── Control (控制)
│   ├── Permissions — 权限管理
│   ├── User Input — 人工介入
│   └── File Checkpointing — 文件回滚
│
└── Operations (运维)
    ├── Cost Tracking — 成本追踪
    ├── Observability — OpenTelemetry 可观测性
    ├── Hosting — 部署托管
    └── Secure Deployment — 安全部署
```

## Agent 循环

```
用户消息
  ↓
系统提示词 + 工具定义
  ↓
Claude API 调用
  ↓
有工具调用? ──是──→ 执行工具 → 结果返回 Claude → 循环
  │
  否
  ↓
流式输出 / 结构化输出
  ↓
返回结果
```

## 会话模型

```
query() / ClaudeSDKClient
├── Session (一个对话)
│   ├── Messages (消息历史)
│   ├── Tool Results (工具结果)
│   └── State (会话状态)
├── 可持久化到外部存储 (Redis, Database)
└── 可恢复/重放
```

---

## 文件索引

### 核心概念
| 文件 | 内容 |
|------|------|
| `details/01-overview.md` | SDK 概述、架构、适用场景 |
| `details/02-quickstart.md` | 快速开始、安装、第一个 Agent |
| `details/03-agent-loop.md` | Agent 循环机制、推理-行动模式 |
| `details/04-claude-code-features.md` | Claude Code 功能集成 |
| `details/05-sessions.md` | 会话管理、创建/恢复/销毁 |
| `details/06-session-storage.md` | 会话持久化、外部存储 |
| `details/07-streaming-input.md` | 流式输入模式 |
| `details/08-user-input.md` | 用户输入处理、人工介入 |
| `details/09-streaming-output.md` | 流式输出、实时响应 |
| `details/10-structured-output.md` | 结构化输出、JSON Schema |

### 工具与扩展
| 文件 | 内容 |
|------|------|
| `details/11-custom-tools.md` | 自定义工具、Python/TypeScript 函数 |
| `details/12-mcp.md` | MCP 协议集成、外部工具 |
| `details/13-tool-search.md` | 工具搜索、大规模工具管理 |
| `details/14-subagents.md` | 子代理、并行执行 |
| `details/15-system-prompts.md` | 系统提示词定制 |
| `details/16-slash-commands.md` | 斜杠命令 |
| `details/17-skills.md` | Agent Skills |
| `details/18-plugins.md` | 插件系统 |
| `details/19-permissions.md` | 权限配置 |
| `details/20-hooks.md` | 生命周期钩子 |

### 运维与部署
| 文件 | 内容 |
|------|------|
| `details/21-file-checkpointing.md` | 文件检查点、回滚 |
| `details/22-cost-tracking.md` | 成本追踪 |
| `details/23-observability.md` | OpenTelemetry 可观测性 |
| `details/24-todo-tracking.md` | Todo 列表 |
| `details/25-hosting.md` | 托管部署 |
| `details/26-secure-deployment.md` | 安全部署 |
| `details/27-python-sdk.md` | Python SDK 完整参考 |
| `details/28-migration-guide.md` | 迁移指南 |

---

## 关键概念速查

| 概念 | 说明 | 关联文件 |
|------|------|----------|
| query() | 单次交互入口函数，返回消息流 | 01-overview, 27-python-sdk |
| ClaudeSDKClient | 多轮连续对话客户端 | 27-python-sdk |
| ClaudeAgentOptions | 配置选项 (model, tools, system_prompt) | 01-overview, 27-python-sdk |
| Session | 会话管理 (session_id, continue, resume, fork) | 05-sessions, 06-session-storage |
| Agent Loop | 推理-行动循环，SDK 核心 | 03-agent-loop |
| Custom Tool | Python/TypeScript 自定义工具 | 11-custom-tools |
| MCP Tool | Model Context Protocol 外部工具 | 12-mcp |
| Tool Search | 大规模工具搜索和发现 | 13-tool-search |
| Subagent | 子代理，独立会话并行执行 | 14-subagents |
| System Prompt | 系统提示词，定义 Agent 行为 | 15-system-prompts |
| Skill | 可复用指令集 | 17-skills |
| Plugin | 功能扩展插件 | 18-plugins |
| Permission Mode | 权限模式 (ask/allow/deny) | 19-permissions |
| Hook | 生命周期钩子 (pre/post tool call) | 20-hooks |
| File Checkpoint | 文件检查点，支持回滚 | 21-file-checkpointing |
| Structured Output | 结构化输出 (JSON Schema) | 10-structured-output |
| Streaming | 流式输入/输出 | 07-streaming-input, 09-streaming-output |

---

## 常用模式速查

### 基本 Agent
```python
from claude_agent_sdk import query, ClaudeAgentOptions

async for message in query(
    prompt="What is 2+2?",
    options=ClaudeAgentOptions(model="claude-sonnet-4-20250514")
):
    print(message)
```

### 自定义工具
```python
from claude_agent_sdk import query, tool, ClaudeAgentOptions

@tool
def search_web(query: str) -> str:
    """Search the web for information."""
    return f"Results for: {query}"

async for message in query(
    prompt="Search for Python tutorials",
    options=ClaudeAgentOptions(tools=[search_web])
):
    print(message)
```

### 流式输出
```python
from claude_agent_sdk import query, ClaudeAgentOptions

async for message in query(
    prompt="Tell me a story",
    options=ClaudeAgentOptions(stream=True)
):
    print(message, end="", flush=True)
```

### 子代理
```python
from claude_agent_sdk import query, subagent, ClaudeAgentOptions

async for message in query(
    prompt="Research Python best practices",
    options=ClaudeAgentOptions(
        subagents=[subagent(name="researcher", prompt="You are a researcher")]
    )
):
    print(message)
```

### 结构化输出
```python
from pydantic import BaseModel
from claude_agent_sdk import query, ClaudeAgentOptions

class Answer(BaseModel):
    answer: str
    confidence: float

async for message in query(
    prompt="What is 2+2?",
    options=ClaudeAgentOptions(output_schema=Answer)
):
    print(message)
```

### MCP 工具
```python
from claude_agent_sdk import query, MCPClient, ClaudeAgentOptions

mcp = MCPClient("filesystem")
async for message in query(
    prompt="Read the README file",
    options=ClaudeAgentOptions(tools=[mcp])
):
    print(message)
```
