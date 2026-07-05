---
title: "LangGraph 测试"
source: "https://langchain-doc.cn/v1/python/langgraph/test.html"
version: "v1"
---

# LangGraph 测试
> 原始文档来源：https://langchain-doc.cn/v1/python/langgraph/test.html

请注意，本指南是特定于LangGraph的，并涵盖了具有自定义结构的图的场景 - 如果你只是刚开始，请查看这部分，它使用LangChain内置的create_agent。

先决条件

首先，确保你已安装pytest：

$ pip install -U pytest

首先，确保你已安装vitest：

$ npm install -D vitest
入门

由于许多LangGraph代理依赖于状态，一个有用的模式是在使用图的每个测试之前创建图，然后在测试中使用新的检查点实例编译它。

下面的例子展示了如何在一个简单的线性图中工作，该图通过node1和node2进行。每个节点都会更新单个状态键my_key：

import pytest
```
from typing_extensions import TypedDict
from langgraph.graph import StateGraph, START, END
from langgraph.checkpoint.memory import MemorySaver
```

```
def create_graph() -> StateGraph:
    class MyState(TypedDict):
        my_key: str
```

```
    graph = StateGraph(MyState)
    graph.add_node("node1", lambda state: {"my_key": "hello from node1"})
    graph.add_node("node2", lambda state: {"my_key": "hello from node2"})
    graph.add_edge(START, "node1")
    graph.add_edge("node1", "node2")
    graph.add_edge("node2", END)
    return graph
```

```
def test_basic_agent_execution() -> None:
    checkpointer = MemorySaver()
    graph = create_graph()
    compiled_graph = graph.compile(checkpointer=checkpointer)
    result = compiled_graph.invoke(
        {"my_key": "initial_value"},
        config={"configurable": {"thread_id": "1"}}
    )
    assert result["my_key"] == "hello from node2"
import { test, expect } from 'vitest';
import {
  StateGraph,
  START,
  END,
  MemorySaver,
```
} from '@langchain/langgraph';
import { z } from "zod/v4";
const State = z.object({
  my_key: z.string(),

const createGraph = () => {
```
  return new StateGraph(State)
    .addNode('node1', (state) => ({ my_key: 'hello from node1' }))
    .addNode('node2', (state) => ({ my_key: 'hello from node2' }))
    .addEdge(START, 'node1')
    .addEdge('node1', 'node2')
    .addEdge('node2', END);
```
};

test('basic agent execution', async () => {
```
  const uncompiledGraph = createGraph();
  const checkpointer = new MemorySaver();
  const compiledGraph = uncompiledGraph.compile({ checkpointer });
  const result = await compiledGraph.invoke(
    { my_key: 'initial_value' },
    { configurable: { thread_id: '1' } }
  );
  expect(result.my_key).toBe('hello from node2');
```
});
测试单个节点和边缘

编译后的LangGraph代理通过graph.nodes暴露对每个单独节点的引用。你可以利用这一点来测试代理中的单个节点。请注意，这将绕过编译图时传递的任何检查点：

import pytest
```
from typing_extensions import TypedDict
from langgraph.graph import StateGraph, START, END
from langgraph.checkpoint.memory import MemorySaver
```

```
def create_graph() -> StateGraph:
    class MyState(TypedDict):
        my_key: str
```

```
    graph = StateGraph(MyState)
    graph.add_node("node1", lambda state: {"my_key": "hello from node1"})
    graph.add_node("node2", lambda state: {"my_key": "hello from node2"})
    graph.add_edge(START, "node1")
    graph.add_edge("node1", "node2")
    graph.add_edge("node2", END)
    return graph
```

```
def test_individual_node_execution() -> None:
    # 在这个例子中会被忽略
    checkpointer = MemorySaver()
    graph = create_graph()
    compiled_graph = graph.compile(checkpointer=checkpointer)
    # 只调用节点1
    result = compiled_graph.nodes["node1"].invoke(
        {"my_key": "initial_value"},
    )
    assert result["my_key"] == "hello from node1"
import { test, expect } from 'vitest';
import {
  StateGraph,
  START,
  END,
  MemorySaver,
```
} from '@langchain/langgraph';
import { z } from "zod/v4";
const State = z.object({
  my_key: z.string(),

const createGraph = () => {
```
  return new StateGraph(State)
    .addNode('node1', (state) => ({ my_key: 'hello from node1' }))
    .addNode('node2', (state) => ({ my_key: 'hello from node2' }))
    .addEdge(START, 'node1')
    .addEdge('node1', 'node2')
    .addEdge('node2', END);
```
};

test('individual node execution', async () => {
```
  const uncompiledGraph = createGraph();
  // 在这个例子中会被忽略
  const checkpointer = new MemorySaver();
  const compiledGraph = uncompiledGraph.compile({ checkpointer });
  // 只调用节点1
  const result = await compiledGraph.nodes['node1'].invoke(
    { my_key: 'initial_value' },
  );
  expect(result.my_key).toBe('hello from node1');
```
});
部分执行

对于由较大图组成的代理，你可能希望测试代理中的部分执行路径，而不是端到端的整个流程。在某些情况下，将这些部分重组为子图在语义上可能更有意义，你可以像正常情况一样孤立地调用它们。

然而，如果你不想更改代理图的整体结构，你可以使用LangGraph的持久化机制来模拟一个状态，其中你的代理恰好在所需部分开始之前暂停，并将在所需部分结束时再次暂停。步骤如下：

使用检查点编译你的代理（内存检查点InMemorySaver足以用于测试）。

调用代理的update_state方法，将as_node参数设置为你要开始测试的节点之前的节点名称。

使用与更新状态时相同的thread_id和设置为你想要停止的节点名称的interrupt_after参数调用代理。

使用检查点编译你的代理（内存检查点MemorySaver足以用于测试）。

调用代理的update_state方法，将asNode参数设置为你要开始测试的节点之前的节点名称。

使用与更新状态时相同的thread_id和设置为你想要停止的节点名称的interruptBefore参数调用代理。

以下是一个仅执行线性图中第二个和第三个节点的示例：

import pytest
```
from typing_extensions import TypedDict
from langgraph.graph import StateGraph, START, END
from langgraph.checkpoint.memory import MemorySaver
```

```
def create_graph() -> StateGraph:
    class MyState(TypedDict):
        my_key: str
```

```
    graph = StateGraph(MyState)
    graph.add_node("node1", lambda state: {"my_key": "hello from node1"})
    graph.add_node("node2", lambda state: {"my_key": "hello from node2"})
    graph.add_node("node3", lambda state: {"my_key": "hello from node3"})
    graph.add_node("node4", lambda state: {"my_key": "hello from node4"})
    graph.add_edge(START, "node1")
    graph.add_edge("node1", "node2")
    graph.add_edge("node2", "node3")
    graph.add_edge("node3", "node4")
    graph.add_edge("node4", END)
    return graph
```

```
def test_partial_execution_from_node2_to_node3() -> None:
    checkpointer = MemorySaver()
    graph = create_graph()
    compiled_graph = graph.compile(checkpointer=checkpointer)
    compiled_graph.update_state(
        config={
          "configurable": {
            "thread_id": "1"
          }
        },
        # 传递给节点2的状态 - 模拟节点1结束时的状态
        values={"my_key": "initial_value"},
        # 更新保存的状态，就好像它来自节点1
        # 执行将在节点2恢复
        as_node="node1",
    )
    result = compiled_graph.invoke(
        # 通过传递None来恢复执行
        None,
        config={"configurable": {"thread_id": "1"}},
        # 在节点3之后停止，这样节点4就不会运行
        interrupt_after="node3",
    )
    assert result["my_key"] == "hello from node3"
import { test, expect } from 'vitest';
import {
  StateGraph,
  START,
  END,
  MemorySaver,
```
} from '@langchain/langgraph';
import { z } from "zod/v4";
const State = z.object({
  my_key: z.string(),

const createGraph = () => {
```
  return new StateGraph(State)
    .addNode('node1', (state) => ({ my_key: 'hello from node1' }))
    .addNode('node2', (state) => ({ my_key: 'hello from node2' }))
    .addNode('node3', (state) => ({ my_key: 'hello from node3' }))
    .addNode('node4', (state) => ({ my_key: 'hello from node4' }))
    .addEdge(START, 'node1')
    .addEdge('node1', 'node2')
    .addEdge('node2', 'node3')
    .addEdge('node3', 'node4')
    .addEdge('node4', END);
```
};

test('partial execution from node2 to node3', async () => {
```
  const uncompiledGraph = createGraph();
  const checkpointer = new MemorySaver();
  const compiledGraph = uncompiledGraph.compile({ checkpointer });
  await compiledGraph.updateState(
    { configurable: { thread_id: '1' } },
    // 传递给节点2的状态 - 模拟节点1结束时的状态
    { my_key: 'initial_value' },
    // 更新保存的状态，就好像它来自节点1
    // 执行将在节点2恢复
    'node1',
  );
  const result = await compiledGraph.invoke(
    // 通过传递null来恢复执行
    null,
    {
      configurable: { thread_id: '1' },
      // 在节点3之后停止，这样节点4就不会运行
      interruptAfter: ['node3']
    },
  );
  expect(result.my_key).toBe('hello from node3');
```
});
