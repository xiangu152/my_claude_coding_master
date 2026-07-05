---
title: "LangGraph 流式传输"
source: "https://langchain-doc.cn/v1/python/langgraph/streaming.html"
version: "v1"
---

# LangGraph 流式传输
> 原始文档来源：https://langchain-doc.cn/v1/python/langgraph/streaming.html

LangGraph实现了一个流式传输系统，用于实时展示更新。流式传输对于增强基于LLM构建的应用程序的响应性至关重要。通过在完整响应准备好之前逐步显示输出，流式传输显著改善了用户体验(UX)，特别是在处理LLM延迟时。 开发工具

LangGraph流式传输可以实现以下功能：

流式传输图状态 — 使用updates和values模式获取状态更新/值。
流式传输子图输出 — 包含父图和任何嵌套子图的输出。
流式传输LLM令牌 — 从任何地方捕获令牌流：节点内部、子图或工具中。
流式传输自定义数据 — 直接从工具函数发送自定义更新或进度信号。
使用多种流式传输模式 — 可以选择values（完整状态）、updates（状态增量）、messages（LLM令牌+元数据）、custom（任意用户数据）或debug（详细跟踪）。
支持的流式传输模式

将以下流式传输模式中的一种或多种作为列表传递给stream或astream方法：

模式	描述
values	在图的每个步骤后流式传输状态的完整值。
updates	在图的每个步骤后流式传输状态的更新。如果在同一步骤中进行了多次更新（例如，运行了多个节点），这些更新会分别流式传输。
custom	从图节点内部流式传输自定义数据。
messages	从调用LLM的任何图节点流式传输2元组（LLM令牌，元数据）。
debug	在图执行过程中流式传输尽可能多的信息。
基本使用示例

LangGraph图暴露了stream（同步）和astream（异步）方法，以迭代器的形式生成流式输出。

```
for chunk in graph.stream(inputs, stream_mode="updates"):
    print(chunk)
for await (const chunk of await graph.stream(inputs, {
  streamMode: "updates",
```
})) {
  console.log(chunk);
扩展示例：流式传输更新
流式传输多种模式

您可以将列表作为stream_mode参数传递，一次流式传输多种模式。 视频直播

流式输出将是元组(mode, chunk)，其中mode是流式传输模式的名称，chunk是该模式流式传输的数据。

```
for mode, chunk in graph.stream(inputs, stream_mode=["updates", "custom"]):
    print(chunk)
```

您可以将数组作为streamMode参数传递，一次流式传输多种模式。

流式输出将是元组[mode, chunk]，其中mode是流式传输模式的名称，chunk是该模式流式传输的数据。

```
for await (const [mode, chunk] of await graph.stream(inputs, {
  streamMode: ["updates", "custom"],
```
})) {
  console.log(chunk);
流式传输图状态

使用流式传输模式updates和values来流式传输图执行时的状态。

updates在图的每个步骤后流式传输状态的更新。
values在图的每个步骤后流式传输状态的完整值。
```
from typing import TypedDict
from langgraph.graph import StateGraph, START, END
```


```
class State(TypedDict):
  topic: str
  joke: str
```


```
def refine_topic(state: State):
    return {"topic": state["topic"] + " and cats"}
```


```
def generate_joke(state: State):
    return {"joke": f"This is a joke about {state['topic']}"}
```

graph = (
```
  StateGraph(State)
  .add_node(refine_topic)
  .add_node(generate_joke)
  .add_edge(START, "refine_topic")
  .add_edge("refine_topic", "generate_joke")
  .add_edge("generate_joke", END)
  .compile()
```
)
```
import { StateGraph, START, END } from "@langchain/langgraph";
import * as z from "zod";
```

const State = z.object({
```
  topic: z.string(),
  joke: z.string(),
```
});

const graph = new StateGraph(State)
```
  .addNode("refineTopic", (state) => {
    return { topic: state.topic + " and cats" };
  })
  .addNode("generateJoke", (state) => {
    return { joke: `This is a joke about ${state.topic}` };
  })
  .addEdge(START, "refineTopic")
  .addEdge("refineTopic", "generateJoke")
  .addEdge("generateJoke", END)
  .compile();
```
使用updates模式

使用此模式仅流式传输每个步骤后节点返回的状态更新。流式输出包括节点名称和更新内容。

```
for chunk in graph.stream(
    {"topic": "ice cream"},
    stream_mode="updates",
```
):
    print(chunk)
```
  { topic: "ice cream" },
  { streamMode: "updates" }
```
)) {
  console.log(chunk);
使用values模式

使用此模式流式传输每个步骤后的完整图状态。

```
for chunk in graph.stream(
    {"topic": "ice cream"},
    stream_mode="values",
```
):
    print(chunk)
```
  { topic: "ice cream" },
  { streamMode: "values" }
```
)) {
  console.log(chunk);
流式传输子图输出

要在流式输出中包含子图的输出，可以在父图的.stream()方法中设置subgraphs=True。这将流式传输父图和任何子图的输出。

输出将以元组(namespace, data)的形式流式传输，其中namespace是包含子图调用节点路径的元组，例如("parent_node:<task_id>", "child_node:<task_id>")。

```
for chunk in graph.stream(
    {"foo": "foo"},
    # 设置subgraphs=True以流式传输子图的输出
    subgraphs=True,
    stream_mode="updates",
```
):
    print(chunk)
要在流式输出中包含子图的输出，可以在父图的.stream()方法中设置subgraphs: true。这将流式传输父图和任何子图的输出。

输出将以元组[namespace, data]的形式流式传输，其中namespace是包含子图调用节点路径的元组，例如["parent_node:<task_id>", "child_node:<task_id>"]。

```
for await (const chunk of await graph.stream(
  { foo: "foo" },
  {
    // 设置subgraphs: true以流式传输子图的输出
    subgraphs: true,
    streamMode: "updates",
  }
```
)) {
  console.log(chunk);
扩展示例：从子图流式传输
调试

使用debug流式传输模式在图执行过程中流式传输尽可能多的信息。流式输出包括节点名称和完整状态。

```
for chunk in graph.stream(
    {"topic": "ice cream"},
    stream_mode="debug",
```
):
    print(chunk)
```
  { topic: "ice cream" },
  { streamMode: "debug" }
```
)) {
  console.log(chunk);
LLM令牌

使用messages流式传输模式从图的任何部分（包括节点、工具、子图或任务）逐令牌流式传输大型语言模型(LLM)的输出。


messages模式的流式输出是元组(message_chunk, metadata)，其中：

message_chunk：来自LLM的令牌或消息段。
metadata：包含图节点和LLM调用详细信息的字典。

如果您的LLM没有可用的LangChain集成，可以使用custom模式代替流式传输其输出。详见与任何LLM一起使用部分。

Python < 3.11中异步需要手动配置
在使用Python < 3.11的异步代码时，必须显式地将RunnableConfig传递给ainvoke()以启用正确的流式传输。详见Python < 3.11中的异步部分获取详细信息，或升级到Python 3.11+。

from dataclasses import dataclass
```
from langchain.chat_models import init_chat_model
from langgraph.graph import StateGraph, START
```


```
@dataclass
class MyState:
    topic: str
    joke: str = ""
```


model = init_chat_model(model="gpt-4o-mini")

```
def call_model(state: MyState):
    """调用LLM生成关于某个主题的笑话"""
    # 注意，即使LLM是使用.invoke而不是.stream运行的，也会发出消息事件
    model_response = model.invoke(
        [
            {"role": "user", "content": f"Generate a joke about {state.topic}"}
        ]
    )
    return {"joke": model_response.content}
```

graph = (
```
    StateGraph(MyState)
    .add_node(call_model)
    .add_edge(START, "call_model")
    .compile()
```
)

```
# "messages"流式传输模式返回元组迭代器(message_chunk, metadata)
# 其中message_chunk是LLM流式传输的令牌，metadata是包含有关LLM调用的图节点信息和其他信息的字典
for message_chunk, metadata in graph.stream(
    {"topic": "ice cream"},
    stream_mode="messages",
```
):
```
    if message_chunk.content:
        print(message_chunk.content, end="|", flush=True)
```

messages模式的流式输出是元组[message_chunk, metadata]，其中：

message_chunk：来自LLM的令牌或消息段。
metadata：包含图节点和LLM调用详细信息的字典。

如果您的LLM没有可用的LangChain集成，可以使用custom模式代替流式传输其输出。详见与任何LLM一起使用部分。

```
import { ChatOpenAI } from "@langchain/openai";
import { StateGraph, START } from "@langchain/langgraph";
import * as z from "zod";
```

const MyState = z.object({
```
  topic: z.string(),
  joke: z.string().default(""),
```
});

const model = new ChatOpenAI({ model: "gpt-4o-mini" });

const callModel = async (state: z.infer<typeof MyState>) => {
  // 调用LLM生成关于某个主题的笑话
  // 注意，即使LLM是使用.invoke而不是.stream运行的，也会发出消息事件
```
  const modelResponse = await model.invoke([
    { role: "user", content: `Generate a joke about ${state.topic}` },
  ]);
  return { joke: modelResponse.content };
```
};

const graph = new StateGraph(MyState)
```
  .addNode("callModel", callModel)
  .addEdge(START, "callModel")
  .compile();
```

// "messages"流式传输模式返回元组迭代器[messageChunk, metadata]
// 其中messageChunk是LLM流式传输的令牌，metadata是包含有关LLM调用的图节点信息和其他信息的字典
```
for await (const [messageChunk, metadata] of await graph.stream(
  { topic: "ice cream" },
  { streamMode: "messages" }
```
)) {
```
  if (messageChunk.content) {
    console.log(messageChunk.content + "|");
  }
```
}
按LLM调用过滤

您可以将tags与LLM调用关联，以按LLM调用过滤流式传输的令牌。

from langchain.chat_models import init_chat_model
# model_1被标记为"joke"
# model_2被标记为"poem"

graph = ... # 定义使用这些LLM的图

```
# stream_mode设置为"messages"以流式传输LLM令牌
# metadata包含有关LLM调用的信息，包括tags
async for msg, metadata in graph.astream(
    {"topic": "cats"},
    stream_mode="messages",
```
):
```
    # 通过metadata中的tags字段过滤流式传输的令牌，仅包含
    # 来自标记为"joke"的LLM调用的令牌
    if metadata["tags"] == ["joke"]:
        print(msg.content, end="|", flush=True)
```
import { ChatOpenAI } from "@langchain/openai";
// model1被标记为"joke"
const model1 = new ChatOpenAI({
```
  model: "gpt-4o-mini",
  tags: ['joke']
```
});
// model2被标记为"poem"
const model2 = new ChatOpenAI({
```
  model: "gpt-4o-mini",
  tags: ['poem']
```
});

const graph = // ... 定义使用这些LLM的图

// streamMode设置为"messages"以流式传输LLM令牌
// metadata包含有关LLM调用的信息，包括tags
```
for await (const [msg, metadata] of await graph.stream(
  { topic: "cats" },
  { streamMode: "messages" }
```
)) {
  // 通过metadata中的tags字段过滤流式传输的令牌，仅包含
  // 来自标记为"joke"的LLM调用的令牌
```
  if (metadata.tags?.includes("joke")) {
    console.log(msg.content + "|");
  }
```
}
扩展示例：按标签过滤
按节点过滤

要仅从特定节点流式传输令牌，请使用stream_mode="messages"并按流式传输的metadata中的langgraph_node字段过滤输出：

```
# "messages"流式传输模式返回(message_chunk, metadata)元组
# 其中message_chunk是LLM流式传输的令牌，metadata是包含有关LLM调用的图节点信息和其他信息的字典
for msg, metadata in graph.stream(
    inputs,
    stream_mode="messages",
```
):
```
    # 通过metadata中的langgraph_node字段过滤流式传输的令牌
    # 仅包含来自指定节点的令牌
    if msg.content and metadata["langgraph_node"] == "some_node_name":
        ...
```
// "messages"流式传输模式返回[messageChunk, metadata]元组
// 其中messageChunk是LLM流式传输的令牌，metadata是包含有关LLM调用的图节点信息和其他信息的字典
```
for await (const [msg, metadata] of await graph.stream(
  inputs,
  { streamMode: "messages" }
```
)) {
  // 通过metadata中的langgraph_node字段过滤流式传输的令牌
  // 仅包含来自指定节点的令牌
```
  if (msg.content && metadata.langgraph_node === "some_node_name") {
    // ...
  }
```
}
扩展示例：从特定节点流式传输LLM令牌
流式传输自定义数据

要从LangGraph节点或工具内部发送自定义用户定义数据，请按照以下步骤操作：

使用get_stream_writer访问流写入器并发出自定义数据。
在调用.stream()或.astream()时设置stream_mode="custom"以在流中获取自定义数据。您可以组合多种模式（例如，["updates", "custom"]），但至少有一种必须是"custom"。

Python < 3.11中异步无法使用get_stream_writer
在Python < 3.11上运行的异步代码中，get_stream_writer将无法工作。
相反，请在您的节点或工具中添加writer参数并手动传递它。
详见Python < 3.11中的异步部分获取使用示例。

在节点中流式传输自定义数据
```
from typing import TypedDict
from langgraph.config import get_stream_writer
from langgraph.graph import StateGraph, START
```

```
class State(TypedDict):
    query: str
    answer: str
```

```
def node(state: State):
    # 获取流写入器以发送自定义数据
    writer = get_stream_writer()
    # 发出自定义键值对（例如，进度更新）
    writer({"custom_key": "Generating custom data inside node"})
    return {"answer": "some data"}
```

graph = (
```
    StateGraph(State)
    .add_node(node)
    .add_edge(START, "node")
    .compile()
```
)

inputs = {"query": "example"}

```
# 设置stream_mode="custom"以在流中接收自定义数据
for chunk in graph.stream(inputs, stream_mode="custom"):
    print(chunk)
```
在工具中流式传输自定义数据
```
from langchain.tools import tool
from langgraph.config import get_stream_writer
```

```
@tool
def query_database(query: str) -> str:
    """查询数据库。"""
    # 访问流写入器以发送自定义数据
    writer = get_stream_writer()
    # 发出自定义键值对（例如，进度更新）
    writer({"data": "Retrieved 0/100 records", "type": "progress"})
    # 执行查询
    # 发出另一个自定义键值对
    writer({"data": "Retrieved 100/100 records", "type": "progress"})
    return "some-answer"
```


graph = ... # 定义使用此工具的图

```
# 设置stream_mode="custom"以在流中接收自定义数据
for chunk in graph.stream(inputs, stream_mode="custom"):
    print(chunk)
```

要从LangGraph节点或工具内部发送自定义用户定义数据，请按照以下步骤操作：

使用LangGraphRunnableConfig中的writer参数发出自定义数据。
在调用.stream()时设置streamMode: "custom"以在流中获取自定义数据。您可以组合多种模式（例如，["updates", "custom"]），但至少有一种必须是"custom"。
在节点中流式传输自定义数据
```
import { StateGraph, START, LangGraphRunnableConfig } from "@langchain/langgraph";
import * as z from "zod";
```

const State = z.object({
```
  query: z.string(),
  answer: z.string(),
```
});

const graph = new StateGraph(State)
```
  .addNode("node", async (state, config) => {
    // 使用writer发出自定义键值对（例如，进度更新）
    config.writer({ custom_key: "Generating custom data inside node" });
    return { answer: "some data" };
  })
  .addEdge(START, "node")
  .compile();
```

const inputs = { query: "example" };

// 设置streamMode: "custom"以在流中接收自定义数据
```
for await (const chunk of await graph.stream(inputs, { streamMode: "custom" })) {
  console.log(chunk);
```
}
在工具中流式传输自定义数据
```
import { tool } from "@langchain/core/tools";
import { LangGraphRunnableConfig } from "@langchain/langgraph";
import * as z from "zod";
```

const queryDatabase = tool(
```
  async (input, config: LangGraphRunnableConfig) => {
    // 使用writer发出自定义键值对（例如，进度更新）
    config.writer({ data: "Retrieved 0/100 records", type: "progress" });
    // 执行查询
    // 发出另一个自定义键值对
    config.writer({ data: "Retrieved 100/100 records", type: "progress" });
    return "some-answer";
  },
  {
    name: "query_database",
    description: "Query the database.",
    schema: z.object({
      query: z.string().describe("The query to execute."),
    }),
  }
```
);

const graph = // ... 定义使用此工具的图

// 设置streamMode: "custom"以在流中接收自定义数据
```
for await (const chunk of await graph.stream(inputs, { streamMode: "custom" })) {
  console.log(chunk);
```
}
与任何LLM一起使用

您可以使用stream_mode="custom"从任何LLM API流式传输数据——即使该API不实现LangChain聊天模型接口。

这使您能够集成原始LLM客户端或提供自己流式传输接口的外部服务，使LangGraph对于自定义设置非常灵活。

from langgraph.config import get_stream_writer
```
def call_arbitrary_model(state):
    """调用任意模型并流式传输输出的示例节点"""
    # 获取流写入器以发送自定义数据
    writer = get_stream_writer()
    # 假设您有一个生成块的流式客户端
    # 使用自定义流式客户端生成LLM令牌
    for chunk in your_custom_streaming_client(state["topic"]):
        # 使用writer将自定义数据发送到流
        writer({"custom_llm_chunk": chunk})
    return {"result": "completed"}
```

graph = (
```
    StateGraph(State)
    .add_node(call_arbitrary_model)
    # 根据需要添加其他节点和边
    .compile()
```
)
```
# 设置stream_mode="custom"以在流中接收自定义数据
for chunk in graph.stream(
    {"topic": "cats"},
    stream_mode="custom",
```
):
```
    # chunk将包含从llm流式传输的自定义数据
    print(chunk)
```

您可以使用streamMode: "custom"从任何LLM API流式传输数据——即使该API不实现LangChain聊天模型接口。

这使您能够集成原始LLM客户端或提供自己流式传输接口的外部服务，使LangGraph对于自定义设置非常灵活。

import { LangGraphRunnableConfig } from "@langchain/langgraph";
const callArbitraryModel = async (
```
  state: any,
  config: LangGraphRunnableConfig
```
) => {
  // 调用任意模型并流式传输输出的示例节点
  // 假设您有一个生成块的流式客户端
  // 使用自定义流式客户端生成LLM令牌
```
  for await (const chunk of yourCustomStreamingClient(state.topic)) {
    // 使用writer将自定义数据发送到流
    config.writer({ custom_llm_chunk: chunk });
  }
  return { result: "completed" };
```
};

const graph = new StateGraph(State)
```
  .addNode("callArbitraryModel", callArbitraryModel)
  // 根据需要添加其他节点和边
  .compile();
```

// 设置streamMode: "custom"以在流中接收自定义数据
```
for await (const chunk of await graph.stream(
  { topic: "cats" },
  { streamMode: "custom" }
```
)) {
  // chunk将包含从llm流式传输的自定义数据
  console.log(chunk);
扩展示例：流式传输任意聊天模型
为特定聊天模型禁用流式传输

如果您的应用程序混合使用支持流式传输和不支持流式传输的模型，则可能需要明确为不支持流式传输的模型禁用流式传输。


在初始化模型时设置disable_streaming=True。

使用init_chat_model
from langchain.chat_models import init_chat_model
model = init_chat_model(
```
    "claude-sonnet-4-5-20250929",
    # 设置disable_streaming=True以禁用聊天模型的流式传输
    disable_streaming=True
```
)
使用聊天模型接口
from langchain_openai import ChatOpenAI
# 设置disable_streaming=True以禁用聊天模型的流式传输

在初始化模型时设置streaming: false。

import { ChatOpenAI } from "@langchain/openai";
const model = new ChatOpenAI({
```
  model: "o1-preview",
  // 设置streaming: false以禁用聊天模型的流式传输
  streaming: false,
```
});
Python < 3.11中的异步

在Python版本< 3.11中，asyncio任务不支持context参数。
这限制了LangGraph自动传播上下文的能力，并在两个关键方面影响LangGraph的流式传输机制：

您必须显式地将RunnableConfig传递给异步LLM调用（例如，ainvoke()），因为回调不会自动传播。
您不能在异步节点或工具中使用get_stream_writer——您必须直接传递writer参数。
扩展示例：带有手动配置的异步LLM调用
