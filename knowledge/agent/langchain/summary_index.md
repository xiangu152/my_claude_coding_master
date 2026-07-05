# LangChain v1 + LangGraph 知识库索引

> 版本：v1 | 文档：https://langchain-doc.cn/v1/python/
> LangChain 是构建 LLM 应用的框架；LangGraph 是基于图的智能体运行时，提供状态管理、持久化、中断等能力。

---

## 核心架构

```
LangChain v1 + LangGraph 应用
├── LangChain (LLM 应用框架)
│   ├── Agent (智能体) — create_agent() 基于 LangGraph 运行时
│   │   ├── Model (LLM 推理)
│   │   ├── Tools (@tool 装饰器 / MCP)
│   │   ├── Messages (Human/AI/System/Tool)
│   │   ├── Memory (短期/长期)
│   │   ├── Middleware (生命周期钩子)
│   │   └── Context Engineering (上下文工程)
│   ├── Retrieval (RAG 检索)
│   ├── Structured Output / Guardrails / Streaming
│   └── Multi-Agent / Human-in-the-Loop
│
└── LangGraph (图运行时)
    ├── Graph (状态图)
    │   ├── Node (节点 — 执行单元)
    │   ├── Edge (边 — 流转条件)
    │   └── State (状态 Schema)
    ├── Persistence (持久化)
    │   ├── Checkpointer (检查点)
    │   └── Durable Execution (持久执行)
    ├── Interrupts (中断 — 人工介入)
    ├── Time Travel (状态回溯)
    ├── Subgraphs (子图)
    ├── Streaming (流式传输)
    └── Pregel (底层运行时)
```

## Agent 核心循环

```
用户输入
  ↓
消息组装 (System + History + User)
  ↓
LLM 推理 (带工具 schema)
  ↓
有工具调用? ──是──→ 执行工具 → 结果返回 LLM → 循环
  │
  否
  ↓
流式输出 / 结构化输出
  ↓
返回结果
```

## 部署架构

```
LangServe / LangGraph Platform
├── Agent 服务化
├── 流式 SSE
├── 人工介入检查点
├── 可观测性 (LangSmith)
└── 测试与评估
```

---

## 文件索引

### 核心概念
| 文件 | 内容 |
|------|------|
| `details/01-quickstart.md` | 快速入门、第一个 Agent |
| `details/02-philosophy.md` | 设计理念、模块化架构 |
| `details/03-agents.md` | 智能体定义、工具调用、ReAct 模式 |
| `details/04-models.md` | 模型集成、ChatModel、Provider 配置 |
| `details/05-messages.md` | 消息类型、消息历史、消息模板 |
| `details/06-tools.md` | @tool 装饰器、工具定义、MCP 集成 |
| `details/07-short-term-memory.md` | 对话记忆、消息窗口、记忆管理 |
| `details/08-streaming.md` | 流式输出、事件流、Token 流 |
| `details/09-middleware.md` | 中间件、生命周期钩子、拦截器 |
| `details/10-structured-output.md` | 结构化输出、JSON Schema、Pydantic 集成 |
| `details/11-guardrails.md` | 输入/输出守卫、安全检查 |
| `details/12-runtime.md` | 运行时配置、环境变量 |
| `details/13-context-engineering.md` | 上下文工程、提示词设计、窗口管理 |
| `details/14-mcp.md` | MCP 协议集成 |
| `details/15-human-in-the-loop.md` | 人工介入、审批流程 |
| `details/16-multi-agent.md` | 多智能体协作、Agent 编排 |
| `details/17-retrieval.md` | RAG 检索、向量存储、文档加载 |
| `details/18-long-term-memory.md` | 长期记忆、持久化存储 |

### 工具与部署
| 文件 | 内容 |
|------|------|
| `details/19-studio.md` | LangChain Studio 可视化 |
| `details/20-test.md` | 测试框架、评估、基准测试 |
| `details/21-deploy.md` | 部署选项 |
| `details/22-ui.md` | Agent 聊天 UI |
| `details/23-observability.md` | 可观测性、LangSmith 追踪 |
| `details/24-install.md` | 安装指南 |

### LangGraph（图运行时）
| 文件 | 内容 |
|------|------|
| `details/25-langgraph-quickstart.md` | LangGraph 快速开始、第一个图 |
| `details/26-langgraph-thinking.md` | 使用 LangGraph 思考、图设计思维 |
| `details/27-langgraph-workflows-agents.md` | 工作流与智能体、图构建模式 |
| `details/28-langgraph-persistence.md` | 持久化、检查点、状态存储 |
| `details/29-langgraph-durable-execution.md` | 持久执行、容错、重试 |
| `details/30-langgraph-streaming.md` | LangGraph 流式传输、Token 流 |
| `details/31-langgraph-interrupts.md` | 中断、人工介入、审批节点 |
| `details/32-langgraph-time-travel.md` | 状态回溯、分支重放 |
| `details/33-langgraph-memory.md` | 内存管理、对话历史 |
| `details/34-langgraph-subgraphs.md` | 子图、模块化图组合 |
| `details/35-langgraph-pregel.md` | Pregel 运行时、底层执行引擎 |
| `details/36-langgraph-local-server.md` | 本地服务器、开发环境 |
| `details/37-langgraph-test.md` | LangGraph 测试 |
| `details/38-langgraph-observability.md` | 可观察性 |

---

## 关键概念速查

| 概念 | 说明 | 关联文件 |
|------|------|----------|
| Agent | 智能体，整合模型/工具/记忆的核心抽象 | 03-agents |
| ChatModel | 对话模型接口，支持流式和工具调用 | 04-models |
| @tool | 工具装饰器，将函数转为可调用工具 | 06-tools |
| Message | 消息类型 (Human/AI/System/Tool) | 05-messages |
| Memory | 记忆管理，短期 (对话) 和长期 (持久化) | 07-short-term-memory, 18-long-term-memory |
| Middleware | 中间件，拦截 agent 生命周期 | 09-middleware |
| StructuredOutput | 结构化输出，Pydantic/JSON Schema | 10-structured-output |
| Retriever | 检索器，RAG 核心组件 | 17-retrieval |
| VectorStore | 向量存储，支持语义搜索 | 17-retrieval |
| Streaming | 流式输出，Token 级实时返回 | 08-streaming |
| Guardrails | 守卫，输入/输出安全检查 | 11-guardrails |
| Context Engineering | 上下文工程，系统提示词设计 | 13-context-engineering |
| MCP | 模型上下文协议，标准化工具集成 | 14-mcp |
| Human-in-the-Loop | 人工介入，审批和确认流程 | 15-human-in-the-loop |
| Multi-Agent | 多智能体协作 | 16-multi-agent |
| LangSmith | 可观测性平台，追踪和调试 | 23-observability |
| **LangGraph** | | |
| Graph | 状态图，LangGraph 核心抽象 | 27-langgraph-workflows-agents |
| Node | 图节点，执行单元 | 27-langgraph-workflows-agents |
| Edge | 图边，条件流转 | 27-langgraph-workflows-agents |
| State | 图状态 Schema (TypedDict) | 27-langgraph-workflows-agents |
| Checkpointer | 检查点，状态持久化 | 28-langgraph-persistence |
| Interrupt | 中断，人工介入点 | 31-langgraph-interrupts |
| Time Travel | 状态回溯，分支重放 | 32-langgraph-time-travel |
| Subgraph | 子图，模块化组合 | 34-langgraph-subgraphs |
| Pregel | 底层运行时引擎 | 35-langgraph-pregel |
| Durable Execution | 持久执行，容错重试 | 29-langgraph-durable-execution |

---

## 常用模式速查

### 基本 Agent
```python
from langchain.agents import create_agent
from langchain.tools import tool

@tool
def search(query: str) -> str:
    """Search the web."""
    return f"Results for: {query}"

agent = create_agent("openai:gpt-5", tools=[search])
result = agent.invoke("What is LangChain?")
```

### 结构化输出
```python
from pydantic import BaseModel
from langchain.agents import create_agent

class Answer(BaseModel):
    answer: str
    confidence: float

agent = create_agent("openai:gpt-5", tools=[], output_schema=Answer)
result = agent.invoke("What is 2+2?")
# result is an Answer instance
```

### 流式输出
```python
for chunk in agent.stream("Tell me a story"):
    print(chunk, end="", flush=True)
```

### RAG 检索
```python
from langchain.retrievers import create_retriever
from langchain.vectorstores import InMemoryVectorStore

store = InMemoryVectorStore.from_documents(docs)
retriever = store.as_retriever()
results = retriever.invoke("What is machine learning?")
```

### MCP 工具集成
```python
from langchain.tools import mcp_tool

tool = mcp_tool(server="filesystem", tool="read_file")
result = tool.invoke({"path": "/tmp/test.txt"})
```
