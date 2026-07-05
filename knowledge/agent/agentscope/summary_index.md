# AgentScope 2.0 知识库索引

> 版本：2.0.3 | 文档：https://docs.agentscope.io/versions/2.0.3/zh
> AgentScope 是生产级多智能体框架，原生支持多租户、多会话和分布式部署。

---

## 核心架构

```
Agent (ReAct 循环引擎)
├── Model (LLM 推理)
├── Tool (外部交互)
│   ├── 内置工具 (Bash, Read, Write, Edit, Glob, Grep, Task*)
│   ├── MCP (Model Context Protocol)
│   ├── FunctionTool (函数包装)
│   └── ToolGroup (分组管理)
├── Middleware (生命周期钩子)
│   ├── on_reply / on_reasoning / on_acting / on_model_call
│   ├── on_system_prompt / on_compress_context
│   └── list_tools
├── Permission (权限控制)
│   ├── Mode: DEFAULT / EXPLORE / ACCEPT_EDITS / BYPASS / DONT_ASK
│   └── Rule: ALLOW / DENY / ASK
├── Context (上下文管理)
│   ├── 压缩 (trigger_ratio → 摘要)
│   ├── 截断 (tool_result_limit)
│   └── Offload (外部存储)
├── Plan (任务规划)
│   └── TaskCreate / TaskGet / TaskList / TaskUpdate
└── State (AgentState 持久化)
```

## 消息与事件

```
Msg (完整消息)
├── TextBlock / DataBlock / ThinkingBlock
├── ToolCallBlock / ToolResultBlock
└── HintBlock

Event (流式事件)
├── 生命周期: ReplyStart/End, ModelCallStart/End
├── 文本流: TextBlockStart/Delta/End
├── 工具流: ToolCallStart/Delta/End, ToolResultStart/Delta/End
└── 人工介入: RequireUserConfirmEvent, RequireExternalExecutionEvent
```

## 部署架构

```
Agent Service (FastAPI)
├── Storage (Redis) — 持久化
├── MessageBus (Redis) — 事件分发
├── WorkspaceManager — 资源隔离
│   ├── LocalWorkspace / DockerWorkspace / E2BWorkspace
│   └── MCP Gateway (容器内 MCP 桥接)
├── Agent Team — 多智能体协作
│   ├── TeamCreate / AgentCreate / TeamSay / TeamDelete
│   └── SubAgentTemplate (角色模板)
└── RAG Service — 知识库管理
    ├── Parser → Chunker → Embedding → VectorStore
    └── 分布式索引 Worker
```

---

## 文件索引

| 文件 | 内容 |
|------|------|
| `details/01-quickstart.md` | 安装、验证、第一个智能体 |
| `details/02-agent.md` | Agent 核心接口、ReAct 循环、reply/reply_stream、状态持久化 |
| `details/03-message-event.md` | Msg 结构、内容块类型、事件生命周期、append_event |
| `details/04-model.md` | ChatModel、Credential、TTS、Embedding、自定义 Provider |
| `details/05-tool.md` | ToolBase 接口、内置工具、FunctionTool、MCP、Skill、ToolGroup |
| `details/06-middleware.md` | 6 个 hook 位置、内置中间件、自定义中间件、执行顺序 |
| `details/07-permission.md` | Permission Mode、Rule、Built-in Checks、Safety check |
| `details/08-context.md` | 上下文压缩、截断、Offload、ContextConfig |
| `details/09-plan.md` | 任务生命周期、依赖表达、存储、自定义任务 |
| `details/10-rag.md` | Parser/Chunker/Embedding/VectorStore、KnowledgeBase、多租户 |
| `details/11-long-term-memory.md` | Mem0Middleware、控制模式、构造方式 |
| `details/12-workspace.md` | Local/Docker/E2B Workspace、Workspace Manager、MCP Gateway |
| `details/13-agent-service.md` | FastAPI 部署、create_app、多租户、Cron 调度 |
| `details/14-agent-team.md` | Leader-Worker 模型、SubAgentTemplate、团队工具 |
| `details/15-rag-service.md` | 多租户 RAG、分布式索引、REST API |
| `details/16-migration.md` | 1.0 → 2.0 破坏性变更 |

---

## 关键概念速查

| 概念 | 说明 | 关联文件 |
|------|------|----------|
| Agent | 无状态推理-行动循环引擎，整合 Model/Tool/Permission/Context/Middleware | 02-agent |
| AgentState | Pydantic 模型，保存上下文/权限/任务/会话状态，可序列化 | 02-agent |
| Credential | API 凭证，一个凭证对应一个 Provider (如 OpenAI/DashScope) | 04-model |
| ChatModel | 对话模型接口，输入消息输出响应，支持流式和工具调用 | 04-model |
| Msg | 完整消息单元，由内容块 (TextBlock/DataBlock/ToolCallBlock 等) 组成 | 03-message-event |
| Event/AgentEvent | 流式事件，reply_stream 产出，支持 start→delta→end 模式 | 03-message-event |
| Toolkit | 工具容器，注册 tool/MCP/skill/ToolGroup，向 LLM 暴露 JSON schema | 05-tool |
| ToolGroup | 带名称的工具集合，通过 reset_tools meta tool 按需激活 | 05-tool |
| FunctionTool | 适配器，将普通 Python 函数包装为 Tool | 05-tool |
| MiddlewareBase | 洋葱式钩子，拦截 agent 6 个生命周期位置 | 06-middleware |
| PermissionContext | 权限上下文，包含 mode (DEFAULT/EXPLORE/ACCEPT_EDITS/BYPASS/DONT_ASK) + rules | 07-permission |
| Offloader | 协议，用于上下文压缩和工具结果卸载到外部存储 | 08-context |
| ContextConfig | 上下文配置，控制压缩阈值 (trigger_ratio) 和工具结果截断 (tool_result_limit) | 08-context |
| KnowledgeBase | RAG 句柄，封装 embedding + vector store + collection | 10-rag |
| Workspace | 执行环境，提供 tool/MCP/skill + offload，支持 Local/Docker/E2B | 12-workspace |
| SubAgentTemplate | 团队中子智能体的可复用蓝图，定义 system_prompt 和权限 | 14-agent-team |
| SSE | Server-Sent Events，Agent Service 流式传输机制 | 13-agent-service |

---

## 常用模式速查

### 基本 Agent
```python
import asyncio
from agentscope.agent import Agent
from agentscope.credential import DashScopeCredential
from agentscope.model import DashScopeChatModel
from agentscope.message import UserMsg
from agentscope.tool import Toolkit, Bash, Read, Write, Edit

async def main():
    agent = Agent(
        name="Friday",
        system_prompt="You are a helpful assistant.",
        model=DashScopeChatModel(
            credential=DashScopeCredential(api_key="YOUR_KEY"),
            model="qwen-plus",
        ),
        toolkit=Toolkit(tools=[Bash(), Read(), Write(), Edit()]),
    )
    result = await agent.reply(UserMsg(name="user", content="Hello"))
    print(result.get_text_content())

asyncio.run(main())
```

### 流式输出
```python
from agentscope.event import EventType

async for event in agent.reply_stream(msg):
    match event.type:
        case EventType.TEXT_BLOCK_DELTA:
            print(event.delta, end="", flush=True)
        case EventType.TOOL_CALL_START:
            print(f"\n[Calling {event.tool_call_name}...]")
```

### 自定义工具
```python
from agentscope.tool import FunctionTool, Toolkit

def get_weather(city: str, unit: str = "celsius") -> str:
    """Get the current weather for a city."""
    return f"The weather in {city} is 22°{unit[0].upper()}"

toolkit = Toolkit(tools=[FunctionTool(get_weather)])
```

### MCP 集成
```python
from agentscope.mcp import MCPClient, StdioMCPConfig

client = MCPClient(
    name="filesystem",
    is_stateful=True,
    mcp_config=StdioMCPConfig(command="mcp-server-filesystem", args=["--root", "/my/project"]),
)
await client.connect()
toolkit = Toolkit(mcps=[client])
```

### 权限配置
```python
from agentscope.permission import PermissionContext, PermissionMode

agent = Agent(..., state=AgentState(
    permission_context=PermissionContext(mode=PermissionMode.DEFAULT)
))
```

### 中间件
```python
from agentscope.middleware import TracingMiddleware, ReplyBudgetControlMiddleware

agent = Agent(..., middlewares=[
    TracingMiddleware(),
    ReplyBudgetControlMiddleware(token_budget=10000),
])
```
