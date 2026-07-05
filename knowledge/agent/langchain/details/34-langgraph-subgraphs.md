---
title: "使用子图"
source: "https://langchain-doc.cn/v1/python/langgraph/use-subgraphs.html"
version: "v1"
---

# 使用子图
> 原始文档来源：https://langchain-doc.cn/v1/python/langgraph/use-subgraphs.html

```
from typing_extensions import TypedDict
from langgraph.graph.state import StateGraph, START
```

```
class State(TypedDict):
    foo: str
```

```
# 子图
def subgraph_node_1(state: State):
    return {"foo": "hi! " + state["foo"]}
```

subgraph_builder = StateGraph(State)
subgraph_builder.add_node(subgraph_node_1)
subgraph_builder.add_edge(START, "subgraph_node_1")
subgraph = subgraph_builder.compile()

# 父图
builder.add_node("node_1", subgraph)
builder.add_edge(START, "node_1")
graph = builder.compile()
定义子图工作流（在下面的示例中为subgraphBuilder）并编译它
在定义父图工作流时，将编译好的子图传递给.addNode方法
```
import { StateGraph, START } from "@langchain/langgraph";
import * as z from "zod";
```

const State = z.object({
  foo: z.string(),

// 子图
const subgraphBuilder = new StateGraph(State)
```
  .addNode("subgraphNode1", (state) => {
    return { foo: "hi! " + state.foo };
  })
  .addEdge(START, "subgraphNode1");
```

const subgraph = subgraphBuilder.compile();

// 父图
const builder = new StateGraph(State)
```
  .addNode("node1", subgraph)
  .addEdge(START, "node1");
```

const graph = builder.compile();
完整示例：共享状态模式
添加持久性

您只需要在编译父图时提供检查点。LangGraph 会自动将检查点传播到子子图。

```
from langgraph.graph import START, StateGraph
from langgraph.checkpoint.memory import MemorySaver
from typing_extensions import TypedDict
```

```
class State(TypedDict):
    foo: str
```

```
# 子图
def subgraph_node_1(state: State):
    return {"foo": state["foo"] + "bar"}
```

subgraph_builder = StateGraph(State)
subgraph_builder.add_node(subgraph_node_1)
subgraph_builder.add_edge(START, "subgraph_node_1")
subgraph = subgraph_builder.compile()

# 父图
builder.add_node("node_1", subgraph)
builder.add_edge(START, "node_1")

checkpointer = MemorySaver()
graph = builder.compile(checkpointer=checkpointer)
```
import { StateGraph, START, MemorySaver } from "@langchain/langgraph";
import * as z from "zod";
```

const State = z.object({
  foo: z.string(),

// 子图
const subgraphBuilder = new StateGraph(State)
```
  .addNode("subgraphNode1", (state) => {
    return { foo: state.foo + "bar" };
  })
  .addEdge(START, "subgraphNode1");
```

const subgraph = subgraphBuilder.compile();

// 父图
const builder = new StateGraph(State)
```
  .addNode("node1", subgraph)
  .addEdge(START, "node1");
```

const checkpointer = new MemorySaver();
const graph = builder.compile({ checkpointer });

如果您希望子图拥有自己的内存，您可以使用适当的检查点选项编译它。这在多代理系统中很有用，如果您希望代理跟踪其内部消息历史：

subgraph_builder = StateGraph(...)
subgraph = subgraph_builder.compile(checkpointer=True)
const subgraphBuilder = new StateGraph(...)
const subgraph = subgraphBuilder.compile({ checkpointer: true });
查看子图状态

当您启用持久性时，您可以通过适当的方法检查图状态（检查点）。要查看子图状态，您可以使用subgraphs选项。


您可以通过graph.get_state(config)检查图状态。要查看子图状态，您可以使用graph.get_state(config, subgraphs=True)。


您可以通过graph.getState(config)检查图状态。要查看子图状态，您可以使用graph.getState(config, { subgraphs: true })。

警告
仅在中断时可用
子图状态只能在子图被中断时查看。一旦您恢复图，您将无法访问子图状态。

查看中断的子图状态
流式传输子图输出

要在流式输出中包含来自子图的输出，您可以在父图的stream方法中设置subgraphs选项。这将流式传输来自父图和任何子图的输出。

```
for chunk in graph.stream(
    {"foo": "foo"},
    subgraphs=True, # 设置为true以流式传输子图输出
    stream_mode="updates",
```
):
    print(chunk)
```
  { foo: "foo" },
  {
    subgraphs: true,   // 设置为true以流式传输子图输出
    streamMode: "updates",
  }
```
)) {
  console.log(chunk);
设置subgraphs: true以流式传输子图输出。
从子图流式传输
