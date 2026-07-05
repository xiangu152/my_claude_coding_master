---
title: "长期记忆"
source: "https://docs.agentscope.io/versions/2.0.3/zh/building-blocks/long-term-memory"
version: "2.0.3"
language: "zh"
---

# 长期记忆

> 原始文档来源：https://docs.agentscope.io/versions/2.0.3/zh/building-blocks/long-term-memory
> AgentScope 2.0.3 中文文档

---
概述
Mem0
安装
快速开始
控制模式
构造方式
关键参数
供 agent 调用的工具

通过基于中间件的长期记忆后端，让知识在多个会话间持久保留


概述
长期记忆让 agent 在多次会话之间保留并检索可持久化的信息，例如用户偏好、历史决策，以及需要在后续对话中反复使用的知识。
在 AgentScope 中，记忆提取和存储服务（如 mem0、ReMe）作为长期记忆后端，以中间件的形式集成到 agent 中。每个记忆中间件是一个 MiddlewareBase 子类，可以按需挂接到 agent 生命周期的 hook 位置。例如：
on_reply —— 在 reply 前检索记忆，在 reply 后把新的对话写回。
on_system_prompt —— 向模型声明记忆工具。
list_tools —— 提供供 agent 调用的 memory 相关工具，例如 search_memory / add_memory。
具体挂接哪些位置由各记忆后端自行决定。
这样无需改动 agent 与 model 代码：只要把记忆中间件传入 Agent(middlewares=[...]) 即可启用长期记忆，并且能与任意其它中间件自由组合。
AgentScope 目前提供以下长期记忆后端，更多后端（如 ReMe）正在规划中：
| 后端 | 类 | 状态 |
| --- | --- | --- |
| mem0 | Mem0Middleware | 可用 |

Mem0
Mem0Middleware 是由 mem0 驱动的、开箱即用的长期记忆后端，同时支持 mem0.AsyncMemory（开源版）与 mem0.AsyncMemoryClient（托管 Platform 版）。在使用 mem0.AsyncMemory（开源版）时，可以让 mem0 自身的记忆抽取与 embedding 都走你现有的 AgentScope 模型 —— 因此 mem0 无需单独的 provider key。

安装
Mem0Middleware 的依赖位于 AgentScope 的可选依赖中：
pip install "agentscope[mem0]"


快速开始
最快捷的方式是直接传入你的 AgentScope chat 与 embedding 模型；中间件会在内部构建一个开源版 mem0 存储，并把记忆抽取与 embedding 都接到这两个模型上。
import asyncio

```python
from agentscope.agent import Agent
from agentscope.middleware import Mem0Middleware
from agentscope.tool import Toolkit
```


```python
async def main():
    mw = Mem0Middleware(
        user_id="alice",
        chat_model=my_chat_model,
        embedding_model=my_embedding_model,
        mode="both",
    )
```

```python
    agent = Agent(
        name="assistant",
        system_prompt="You are a helpful assistant.",
        model=my_chat_model,
        toolkit=Toolkit(tools=await mw.list_tools()),
        middlewares=[mw],
    )
```

```python
    # 本次会话写入的记忆，会在后续相同 ``user_id`` 的会话中重新出现。
    await agent(...)
```


asyncio.run(main())

Mem0Middleware 通过 list_tools() 提供 search_memory / add_memory 工具，而 agent 不会自动调用它。要让这些工具对 agent 可用，需要你自己收集并传入 toolkit —— Toolkit(tools=await mw.list_tools())。在 static_control 模式下 list_tools() 返回空列表。

控制模式
mode 参数决定 agent 与 mem0 的交互方式，默认为 "both"，与 AgentScope 1.x 的 ReActAgent.long_term_memory_mode 一致。
模式	行为
static_control	中间件在每次 reply 前检索 mem0，把检索到的记忆以 AssistantMsg(name="memory") 注入上下文，并在 reply 后把新的对话写回。agent 对 mem0 无感知。
agent_control	中间件暴露 search_memory / add_memory 工具，并在 system prompt 中追加一段简短的使用提示。由 agent 自行决定何时读写记忆；没有自动检索或写回。
both	两种模式同时生效 —— 既自动检索，又提供按需调用的工具。

构造方式
Mem0Middleware 支持三种接入 mem0 后端的方式：
AgentScope 模型
模型 + 自定义 config
预构建 client
传入 AgentScope 模型，让中间件在内部构建开源版 AsyncMemory（mem0 默认的 Qdrant 存储）。embedding 模型的 dimensions 必须与向量存储匹配（默认 Qdrant 期望 1536）。
Mem0Middleware(
    user_id="alice",
    chat_model=my_chat_model,
    embedding_model=my_embedding_model,
)

Mem0Middleware 要求使用异步 mem0 client（mem0.AsyncMemory 或 mem0.AsyncMemoryClient）。同步的 Memory / MemoryClient 不受支持。

关键参数
| 参数 | 类型 | 默认值 | 说明 |
| --- | --- | --- | --- |
| user_id | str | （必填） | 用户记忆的 mem0 命名空间。 |
| mode | "static_control" | "agent_control" | "both" | "both" | agent 与 mem0 的交互方式（见上文）。 |
| agent_id | str | None | None | 可选的更细粒度命名空间。 |
| top_k | int | 5 | 每次 static-control 检索返回的最大记忆数；同时作为 search_memory 工具的默认值。 |
| threshold | float | None | None | 最小相似度分数；None 表示交由 mem0 决定。 |
| scope_search_by_agent | bool | True | 为 True 时检索同时按 user_id 与 agent_id 过滤；为 False 时同一用户的记忆在多个 agent 间共享。 |
| await_write | bool | True | 为 True 时回合结束后的写入会内联等待；为 False 时为异步触发（更快，但异常只在日志中体现）。 |

供 agent 调用的工具
在 agent_control 与 both 模式下，中间件提供两个模型可按需调用的工具：
search_memory(keywords, limit=5) —— 用一组简短、精准的关键词检索记忆。每个关键词作为独立查询发起，结果合并并去重。
add_memory(thinking, content) —— 记录持久信息。只有 content（一组独立完整的句子）会被写入 mem0；thinking 留在对话记录中以便审计。
两个工具都会自动放行（auto-allow），并直接从中间件实例读取 user_id / agent_id，因此除了把它们加入 toolkit 之外无需额外接线。
RAG
