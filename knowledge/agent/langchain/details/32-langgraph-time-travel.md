---
title: "使用状态回溯"
source: "https://langchain-doc.cn/v1/python/langgraph/use-time-travel.html"
version: "v1"
---

# 使用状态回溯
> 原始文档来源：https://langchain-doc.cn/v1/python/langgraph/use-time-travel.html

LangGraph提供了时间旅行功能来支持这些用例。具体来说，您可以从先前的检查点恢复执行 - 无论是重放相同状态还是修改它以探索替代方案。在所有情况下，恢复过去的执行都会在历史中产生一个新的分支。

要在LangGraph中使用时间旅行：

运行图，使用@[invoke][CompiledStateGraph.invoke]或@[stream][CompiledStateGraph.stream]方法与初始输入。
在现有线程中识别检查点：使用@[get_state_history]方法检索特定thread_id的执行历史并找到所需的checkpoint_id。
或者，在您希望执行暂停的节点之前设置中断。然后您可以找到截至该中断记录的最新检查点。
更新图状态（可选）：使用@[update_state]方法修改检查点处的图状态，并从替代状态恢复执行。
从检查点恢复执行：使用invoke或stream方法，输入为None，配置包含适当的thread_id和checkpoint_id。
运行图，使用@[invoke][CompiledStateGraph.invoke]或@[stream][CompiledStateGraph.stream]方法与初始输入。
在现有线程中识别检查点：使用@[getStateHistory]方法检索特定thread_id的执行历史并找到所需的checkpoint_id。
或者，在您希望执行暂停的节点之前设置断点。然后您可以找到截至该断点记录的最新检查点。
更新图状态（可选）：使用@[updateState]方法修改检查点处的图状态，并从替代状态恢复执行。
从检查点恢复执行：使用invoke或stream方法，输入为null，配置包含适当的thread_id和checkpoint_id。

有关时间旅行的概念概述，请参阅时间旅行。

在工作流中

这个示例构建了一个简单的LangGraph工作流，生成笑话主题并使用LLM编写笑话。它演示了如何运行图，检索过去的执行检查点，可选地修改状态，以及从选定的检查点恢复执行以探索替代结果。

设置

首先，我们需要安装所需的包

%%capture --no-stderr
pip install --quiet -U langgraph langchain_anthropic
npm install @langchain/langgraph @langchain/anthropic

接下来，我们需要设置Anthropic（我们将使用的LLM）的API密钥

```
import getpass
import os
```


```
def _set_env(var: str):
    if not os.environ.get(var):
        os.environ[var] = getpass.getpass(f"{var}: ")
```


_set_env("ANTHROPIC_API_KEY")
process.env.ANTHROPIC_API_KEY = "YOUR_API_KEY";

注册LangSmith以快速发现问题并提高您的LangGraph项目的性能。LangSmith允许您使用跟踪数据调试、测试和监控使用LangGraph构建的LLM应用程序。

import uuid
```
from typing_extensions import TypedDict, NotRequired
from langgraph.graph import StateGraph, START, END
from langchain.chat_models import init_chat_model
from langgraph.checkpoint.memory import InMemorySaver
```


```
class State(TypedDict):
    topic: NotRequired[str]
    joke: NotRequired[str]
```


model = init_chat_model(
```
    "claude-sonnet-4-5-20250929",
    temperature=0,
```
)


```
def generate_topic(state: State):
    """LLM调用来生成笑话主题"""
    msg = model.invoke("Give me a funny topic for a joke")
    return {"topic": msg.content}
```


```
def write_joke(state: State):
    """LLM调用来基于主题写笑话"""
    msg = model.invoke(f"Write a short joke about {state['topic']}")
    return {"joke": msg.content}
```


# 构建工作流

# 添加节点
workflow.add_node("write_joke", write_joke)

# 添加边连接节点
workflow.add_edge("generate_topic", "write_joke")
workflow.add_edge("write_joke", END)

# 编译
graph = workflow.compile(checkpointer=checkpointer)
graph
```
import { v4 as uuidv4 } from "uuid";
import * as z from "zod";
import { StateGraph, START, END } from "@langchain/langgraph";
import { ChatAnthropic } from "@langchain/anthropic";
import { MemorySaver } from "@langchain/langgraph";
```

const State = z.object({
```
  topic: z.string().optional(),
  joke: z.string().optional(),
```
});

const model = new ChatAnthropic({
```
  model: "claude-sonnet-4-5-20250929",
  temperature: 0,
```
});

// 构建工作流
const workflow = new StateGraph(State)
  // 添加节点
```
  .addNode("generateTopic", async (state) => {
    // LLM调用来生成笑话主题
    const msg = await model.invoke("Give me a funny topic for a joke");
    return { topic: msg.content };
  })
  .addNode("writeJoke", async (state) => {
    // LLM调用来基于主题写笑话
    const msg = await model.invoke(`Write a short joke about ${state.topic}`);
    return { joke: msg.content };
  })
  // 添加边连接节点
  .addEdge(START, "generateTopic")
  .addEdge("generateTopic", "writeJoke")
  .addEdge("writeJoke", END);
```

// 编译
const checkpointer = new MemorySaver();
const graph = workflow.compile({ checkpointer });
1. 运行图
config = {
```
    "configurable": {
        "thread_id": uuid.uuid4(),
    }
```
}
state = graph.invoke({}, config)

```
print(state["topic"])
print()
print(state["joke"])
```
const config = {
```
  configurable: {
    thread_id: uuidv4(),
  },
```
};

const state = await graph.invoke({}, config);

console.log(state.topic);
console.log();
console.log(state.joke);

输出：

How about "The Secret Life of Socks in the Dryer"? You know, exploring the mysterious phenomenon of how socks go into the laundry as pairs but come out as singles. Where do they go? Are they starting new lives elsewhere? Is there a sock paradise we don't know about? There's a lot of comedic potential in the everyday mystery that unites us all!

# The Secret Life of Socks in the Dryer
I finally discovered where all my missing socks go after the dryer. Turns out they're not missing at all—they've just eloped with someone else's socks from the laundromat to start new lives together.

My blue argyle is now living in Bermuda with a red polka dot, posting vacation photos on Sockstagram and sending me lint as alimony.
2. 在现有线程中识别检查点
# 状态以倒序时间顺序返回。

```
for state in states:
    print(state.next)
    print(state.config["configurable"]["checkpoint_id"])
    print()
```

输出：

()
1f02ac4a-ec9f-6524-8002-8f7b0bbeed0e

('write_joke',)
1f02ac4a-ce2a-6494-8001-cb2e2d651227

('generate_topic',)
1f02ac4a-a4e0-630d-8000-b73c254ba748

('__start__',)
1f02ac4a-a4dd-665e-bfff-e6c8c44315d9
// 状态以倒序时间顺序返回。
const states = [];
```
for await (const state of graph.getStateHistory(config)) {
  states.push(state);
```
}

```
for (const state of states) {
  console.log(state.next);
  console.log(state.config.configurable?.checkpoint_id);
  console.log();
```
}

输出：

[]
1f02ac4a-ec9f-6524-8002-8f7b0bbeed0e

['writeJoke']
1f02ac4a-ce2a-6494-8001-cb2e2d651227

['generateTopic']
1f02ac4a-a4e0-630d-8000-b73c254ba748

['__start__']
1f02ac4a-a4dd-665e-bfff-e6c8c44315d9
# 这是倒数第二个状态（状态按时间顺序列出）
```
print(selected_state.next)
print(selected_state.values)
```

输出：

('write_joke',)
{'topic': 'How about "The Secret Life of Socks in the Dryer"? You know, exploring the mysterious phenomenon of how socks go into the laundry as pairs but come out as singles. Where do they go? Are they starting new lives elsewhere? Is there a sock paradise we don\'t know about? There\'s a lot of comedic potential in the everyday mystery that unites us all!'}
// 这是倒数第二个状态（状态按时间顺序列出）
const selectedState = states[1];
console.log(selectedState.next);
console.log(selectedState.values);

输出：

['writeJoke']
{'topic': 'How about "The Secret Life of Socks in the Dryer"? You know, exploring the mysterious phenomenon of how socks go into the laundry as pairs but come out as singles. Where do they go? Are they starting new lives elsewhere? Is there a sock paradise we don\'t know about? There\'s a lot of comedic potential in the everyday mystery that unites us all!'}
3. 更新图状态（可选）
@[`update_state`]将创建一个新的检查点。新检查点将与同一线程关联，但会有一个新的检查点ID。
new_config = graph.update_state(selected_state.config, values={"topic": "chickens"})
print(new_config)
输出：

{'configurable': {'thread_id': 'c62e2e03-c27b-4cb6-8cea-ea9bfedae006', 'checkpoint_ns': '', 'checkpoint_id': '1f02ac4a-ecee-600b-8002-a1d21df32e4c'}}

updateState将创建一个新的检查点。新检查点将与同一线程关联，但会有一个新的检查点ID。

const newConfig = await graph.updateState(selectedState.config, {
  topic: "chickens",
console.log(newConfig);

输出：

{'configurable': {'thread_id': 'c62e2e03-c27b-4cb6-8cea-ea9bfedae006', 'checkpoint_ns': '', 'checkpoint_id': '1f02ac4a-ecee-600b-8002-a1d21df32e4c'}}
4. 从检查点恢复执行
graph.invoke(None, new_config)

输出：

{'topic': 'chickens',
 'joke': 'Why did the chicken join a band?\n\nBecause it had excellent drumsticks!'}
await graph.invoke(null, newConfig);
输出：

{
```
  'topic': 'chickens',
  'joke': 'Why did the chicken join a band?\n\nBecause it had excellent drumsticks!'
```
}
