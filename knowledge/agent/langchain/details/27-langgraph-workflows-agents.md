---
title: "工作流与智能体"
source: "https://langchain-doc.cn/v1/python/langgraph/workflows-agents.html"
version: "v1"
---

# 工作流与智能体
> 原始文档来源：https://langchain-doc.cn/v1/python/langgraph/workflows-agents.html

from langchain_anthropic import ChatAnthropic
# 初始化 LLM

对于 JavaScript：

import { ChatAnthropic } from "@langchain/anthropic";
// 初始化 LLM
const llm = new ChatAnthropic({
  model: "claude-3-opus-20240229",
结构化输出

在构建工作流时，我们经常需要从 LLM 获取结构化输出。我们可以使用 Pydantic 来定义输出模式。

from pydantic import BaseModel, Field
```
# 定义结构化输出的模型
class MultiplierResponse(BaseModel):
    result: int = Field(description="乘法运算的结果")
```

# 使用结构化输出增强 LLM

# 调用 LLM 并获取结构化输出
print(response.result)  # 输出: 15
对于 TypeScript，我们使用 Zod 来定义模式：

import * as z from "zod";
// 定义结构化输出的模型
const multiplierResponseSchema = z.object({
  result: z.number().describe("乘法运算的结果"),

// 使用结构化输出增强 LLM
const llmWithStructuredOutput = llm.withStructuredOutput(multiplierResponseSchema);

// 调用 LLM 并获取结构化输出
const response = await llmWithStructuredOutput.invoke("计算 5 乘以 3 的结果");
console.log(response.result);  // 输出: 15
工具

工具是可以由 LLM 或工作流调用的函数。它们允许我们扩展 LLM 的能力，使其能够执行计算、访问外部系统等。

from langchain.tools import tool
```
# 定义一个工具
@tool
def multiply(a: int, b: int) -> int:
    """计算 `a` 和 `b` 的乘积。
```

```
    Args:
        a: 第一个整数
        b: 第二个整数
    """
    return a * b
```

# 使用工具
print(tool_result)  # 输出: 15
对于 TypeScript：

```
import { tool } from "@langchain/core/tools";
import * as z from "zod";
```

// 定义一个工具
const multiply = tool(
```
  ({ a, b }) => {
    return a * b;
  },
  {
    name: "multiply",
    description: "计算两个数字的乘积",
    schema: z.object({
      a: z.number().describe("第一个数字"),
      b: z.number().describe("第二个数字"),
    }),
  }
```
);

// 使用工具
const toolResult = await multiply.invoke({ a: 5, b: 3 });
console.log(toolResult);  // 输出: 15
常见工作流模式

以下是一些常见的工作流模式及其使用场景：

提示链

提示链是按顺序执行的一系列提示。它们适用于需要将一个步骤的输出作为下一个步骤的输入的情况。

prompt_chain.png

让我们构建一个简单的提示链，用于生成一个笑话，然后检查笑点是否合适，如果不合适则改进它：

```
from typing import Annotated
from langgraph.graph import StateGraph, START, END
from langgraph.types import StateGraph
from pydantic import BaseModel, Field
from typing_extensions import TypedDict, Literal
from operator import add
```

```
# 定义图状态
class State(TypedDict):
    joke: str
    topic: str
    punchline: str
    needs_improvement: bool
```

```
# 定义用于评估笑话的结构化输出模型
class PunchlineEvaluation(BaseModel):
    needs_improvement: bool = Field(
        description="判断笑话是否需要改进。如果笑点明显或平庸，返回 true。"
    )
```

# 使用结构化输出增强 LLM

# 节点
```
def generate_joke(state: State):
    """生成一个关于给定主题的笑话"""
    joke = llm.invoke(f"写一个关于 {state['topic']} 的笑话")
    return {"joke": joke.content}
```

```
def check_punchline(state: State):
    """检查笑话是否需要改进"""
    evaluation = evaluator.invoke(f"评估这个笑话的笑点: {state['joke']}")
    return {"needs_improvement": evaluation.needs_improvement}
```

```
def improve_joke(state: State):
    """改进笑话"""
    improved_joke = llm.invoke(f"改进这个笑话，使其更有趣: {state['joke']}")
    return {"joke": improved_joke.content}
```

```
def polish_joke(state: State):
    """润色笑话，确保质量"""
    polished_joke = llm.invoke(f"润色这个笑话，使其更加完美: {state['joke']}")
    return {"joke": polished_joke.content}
```

# 条件边函数，根据笑话是否需要改进来决定路由
```
def should_improve_joke(state: State):
    """根据笑话是否需要改进来决定路由"""
    if state["needs_improvement"]:
        return "improve_joke"
    else:
        return "polish_joke"
```

# 构建工作流

# 添加节点
workflow_builder.add_node("check_punchline", check_punchline)
workflow_builder.add_node("improve_joke", improve_joke)
workflow_builder.add_node("polish_joke", polish_joke)

# 添加边来连接节点
workflow_builder.add_edge("generate_joke", "check_punchline")
workflow_builder.add_conditional_edges(
```
    "check_punchline",
    should_improve_joke,
    {  # 由 should_improve_joke 返回的值 : 要访问的下一个节点的名称
        "improve_joke": "improve_joke",
        "polish_joke": "polish_joke"
    }
```
)
workflow_builder.add_edge("improve_joke", "polish_joke")
workflow_builder.add_edge("polish_joke", END)

# 编译工作流

```
# 显示工作流
from IPython.display import display, Image
```
display(Image(workflow.get_graph().draw_mermaid_png()))

# 调用
print(state["joke"])
对于 Functional API：

```
from langgraph.graph import task, entrypoint
from pydantic import BaseModel, Field
from typing import Optional
```

```
# 定义用于评估笑话的结构化输出模型
class PunchlineEvaluation(BaseModel):
    needs_improvement: bool = Field(
        description="判断笑话是否需要改进。如果笑点明显或平庸，返回 true。"
    )
```

# 使用结构化输出增强 LLM

```
# 节点
@task
def generate_joke(topic: str):
    """生成一个关于给定主题的笑话"""
    joke = llm.invoke(f"写一个关于 {topic} 的笑话")
    return joke.content
```

```
@task
def check_punchline(joke: str):
    """检查笑话是否需要改进"""
    evaluation = evaluator.invoke(f"评估这个笑话的笑点: {joke}")
    return evaluation.needs_improvement
```

```
@task
def improve_joke(joke: str):
    """改进笑话"""
    improved_joke = llm.invoke(f"改进这个笑话，使其更有趣: {joke}")
    return improved_joke.content
```

```
@task
def polish_joke(joke: str):
    """润色笑话，确保质量"""
    polished_joke = llm.invoke(f"润色这个笑话，使其更加完美: {joke}")
    return polished_joke.content
```

```
@entrypoint()
def workflow(topic: str):
    joke = generate_joke(topic).result()
    needs_improvement = check_punchline(joke).result()
    
    if needs_improvement:
        joke = improve_joke(joke).result()
    
    joke = polish_joke(joke).result()
    return joke
```

# 调用
print(joke)
```
# 流式处理
for step in workflow.stream("编程", stream_mode="updates"):
    print(step)
    print("\n")
```

对于 TypeScript：

```
import { Annotation, StateGraph } from "@langchain/langgraph";
import * as z from "zod";
```

// 定义图状态
const StateAnnotation = Annotation.Root({
```
  joke: Annotation<string>,
  topic: Annotation<string>,
  punchline: Annotation<string>,
  needsImprovement: Annotation<boolean>,
```
});

// 定义用于评估笑话的结构化输出模型
const punchlineEvaluationSchema = z.object({
```
  needsImprovement: z.boolean().describe(
    "判断笑话是否需要改进。如果笑点明显或平庸，返回 true。"
  ),
```
});

// 使用结构化输出增强 LLM
const evaluator = llm.withStructuredOutput(punchlineEvaluationSchema);

// 节点
async function generateJoke(state: typeof StateAnnotation.State) {
  // 生成一个关于给定主题的笑话
```
  const joke = await llm.invoke(`写一个关于 ${state.topic} 的笑话`);
  return { joke: joke.content };
```
}

async function checkPunchline(state: typeof StateAnnotation.State) {
  // 检查笑话是否需要改进
```
  const evaluation = await evaluator.invoke(`评估这个笑话的笑点: ${state.joke}`);
  return { needsImprovement: evaluation.needsImprovement };
```
}

async function improveJoke(state: typeof StateAnnotation.State) {
  // 改进笑话
```
  const improvedJoke = await llm.invoke(`改进这个笑话，使其更有趣: ${state.joke}`);
  return { joke: improvedJoke.content };
```
}

async function polishJoke(state: typeof StateAnnotation.State) {
  // 润色笑话，确保质量
```
  const polishedJoke = await llm.invoke(`润色这个笑话，使其更加完美: ${state.joke}`);
  return { joke: polishedJoke.content };
```
}

// 条件边函数，根据笑话是否需要改进来决定路由
function shouldImproveJoke(state: typeof StateAnnotation.State) {
  // 根据笑话是否需要改进来决定路由
```
  if (state.needsImprovement) {
    return "improveJoke";
  } else {
    return "polishJoke";
  }
```
}

// 构建工作流
const workflow = new StateGraph(StateAnnotation)
```
  .addNode("generateJoke", generateJoke)
  .addNode("checkPunchline", checkPunchline)
  .addNode("improveJoke", improveJoke)
  .addNode("polishJoke", polishJoke)
  .addEdge("__start__", "generateJoke")
  .addEdge("generateJoke", "checkPunchline")
  .addConditionalEdges(
    "checkPunchline",
    shouldImproveJoke,
    {
      "improveJoke": "improveJoke",
      "polishJoke": "polishJoke"
    }
  )
  .addEdge("improveJoke", "polishJoke")
  .addEdge("polishJoke", "__end__")
  .compile();
```

// 调用
const state = await workflow.invoke({ topic: "编程" });
console.log(state.joke);

// 流式处理
const stream = await workflow.stream({ topic: "编程" }, {
  streamMode: "updates",

```
for await (const step of stream) {
  console.log(step);
  console.log("\n");
```
}

对于 TypeScript Functional API：

```
import { task, entrypoint } from "@langchain/langgraph";
import * as z from "zod";
```

// 定义用于评估笑话的结构化输出模型
const punchlineEvaluationSchema = z.object({
```
  needsImprovement: z.boolean().describe(
    "判断笑话是否需要改进。如果笑点明显或平庸，返回 true。"
  ),
```
});

// 使用结构化输出增强 LLM
const evaluator = llm.withStructuredOutput(punchlineEvaluationSchema);

// 节点
const generateJoke = task("generateJoke", async (topic: string) => {
  // 生成一个关于给定主题的笑话
```
  const joke = await llm.invoke(`写一个关于 ${topic} 的笑话`);
  return joke.content;
```
});

const checkPunchline = task("checkPunchline", async (joke: string) => {
  // 检查笑话是否需要改进
```
  const evaluation = await evaluator.invoke(`评估这个笑话的笑点: ${joke}`);
  return evaluation.needsImprovement;
```
});

const improveJoke = task("improveJoke", async (joke: string) => {
  // 改进笑话
```
  const improvedJoke = await llm.invoke(`改进这个笑话，使其更有趣: ${joke}`);
  return improvedJoke.content;
```
});

const polishJoke = task("polishJoke", async (joke: string) => {
  // 润色笑话，确保质量
```
  const polishedJoke = await llm.invoke(`润色这个笑话，使其更加完美: ${joke}`);
  return polishedJoke.content;
```
});

// 构建工作流
const workflow = entrypoint(
  "jokeWorkflow",
```
  async (topic: string) => {
    let joke = await generateJoke(topic);
    const needsImprovement = await checkPunchline(joke);
    
    if (needsImprovement) {
      joke = await improveJoke(joke);
    }
    
    joke = await polishJoke(joke);
    return joke;
  }
```
);

// 调用
const joke = await workflow.invoke("编程");
console.log(joke);

// 流式处理
const stream = await workflow.stream("编程", {
  streamMode: "updates",

```
for await (const step of stream) {
  console.log(step);
  console.log("\n");
```
}
并行化

并行化工作流允许我们同时执行多个任务，这对于需要处理大量数据或进行多个独立计算的情况非常有用。

parallelization.png

让我们构建一个简单的并行工作流，同时调用多个 LLM 并聚合结果：

```
from typing import Annotated
from langgraph.graph import StateGraph, START, END
from operator import add
from langgraph.types import StateGraph
from typing_extensions import TypedDict
```

```
# 定义图状态
class State(TypedDict):
    prompt: str
    result_1: str
    result_2: str
    result_3: str
    final_result: str
```

```
# 节点
def call_llm_1(state: State):
    """调用第一个 LLM"""
    result = llm.invoke(f"从角度 1 回答: {state['prompt']}")
    return {"result_1": result.content}
```

```
def call_llm_2(state: State):
    """调用第二个 LLM"""
    result = llm.invoke(f"从角度 2 回答: {state['prompt']}")
    return {"result_2": result.content}
```

```
def call_llm_3(state: State):
    """调用第三个 LLM"""
    result = llm.invoke(f"从角度 3 回答: {state['prompt']}")
    return {"result_3": result.content}
```

```
def aggregator(state: State):
    """聚合三个 LLM 的结果"""
    return {
        "final_result": f"结果 1: {state['result_1']}\n\n结果 2: {state['result_2']}\n\n结果 3: {state['result_3']}"
    }
```

# 构建工作流

# 添加节点
workflow_builder.add_node("call_llm_2", call_llm_2)
workflow_builder.add_node("call_llm_3", call_llm_3)
workflow_builder.add_node("aggregator", aggregator)

# 添加边来连接节点
workflow_builder.add_edge(START, "call_llm_2")
workflow_builder.add_edge(START, "call_llm_3")
workflow_builder.add_edge("call_llm_1", "aggregator")
workflow_builder.add_edge("call_llm_2", "aggregator")
workflow_builder.add_edge("call_llm_3", "aggregator")
workflow_builder.add_edge("aggregator", END)

# 编译工作流

```
# 显示工作流
from IPython.display import display, Image
```
display(Image(workflow.get_graph().draw_mermaid_png()))

# 调用
print(state["final_result"])
对于 Functional API：

```
from langgraph.graph import task, entrypoint
import asyncio
```

```
# 节点
@task
def call_llm_1(prompt: str):
    """调用第一个 LLM"""
    result = llm.invoke(f"从角度 1 回答: {prompt}")
    return result.content
```

```
@task
def call_llm_2(prompt: str):
    """调用第二个 LLM"""
    result = llm.invoke(f"从角度 2 回答: {prompt}")
    return result.content
```

```
@task
def call_llm_3(prompt: str):
    """调用第三个 LLM"""
    result = llm.invoke(f"从角度 3 回答: {prompt}")
    return result.content
```

```
@entrypoint()
def parallel_workflow(prompt: str):
    # 并行调用三个 LLM
    result_1_future = call_llm_1(prompt)
    result_2_future = call_llm_2(prompt)
    result_3_future = call_llm_3(prompt)
    
    # 等待所有结果
    result_1 = result_1_future.result()
    result_2 = result_2_future.result()
    result_3 = result_3_future.result()
    
    # 聚合结果
    return f"结果 1: {result_1}\n\n结果 2: {result_2}\n\n结果 3: {result_3}"
```

# 调用
print(result)
```
# 流式处理
for step in parallel_workflow.stream("什么是人工智能？", stream_mode="updates"):
    print(step)
    print("\n")
```

对于 TypeScript：

import { Annotation, StateGraph } from "@langchain/langgraph";
// 定义图状态
const StateAnnotation = Annotation.Root({
```
  prompt: Annotation<string>,
  result1: Annotation<string>,
  result2: Annotation<string>,
  result3: Annotation<string>,
  finalResult: Annotation<string>,
```
});

// 节点
async function callLlm1(state: typeof StateAnnotation.State) {
  // 调用第一个 LLM
```
  const result = await llm.invoke(`从角度 1 回答: ${state.prompt}`);
  return { result1: result.content };
```
}

async function callLlm2(state: typeof StateAnnotation.State) {
  // 调用第二个 LLM
```
  const result = await llm.invoke(`从角度 2 回答: ${state.prompt}`);
  return { result2: result.content };
```
}

async function callLlm3(state: typeof StateAnnotation.State) {
  // 调用第三个 LLM
```
  const result = await llm.invoke(`从角度 3 回答: ${state.prompt}`);
  return { result3: result.content };
```
}

async function aggregator(state: typeof StateAnnotation.State) {
  // 聚合三个 LLM 的结果
```
  return {
    finalResult: `结果 1: ${state.result1}\n\n结果 2: ${state.result2}\n\n结果 3: ${state.result3}`
  };
```
}

// 构建工作流
const workflow = new StateGraph(StateAnnotation)
```
  .addNode("callLlm1", callLlm1)
  .addNode("callLlm2", callLlm2)
  .addNode("callLlm3", callLlm3)
  .addNode("aggregator", aggregator)
  .addEdge("__start__", "callLlm1")
  .addEdge("__start__", "callLlm2")
  .addEdge("__start__", "callLlm3")
  .addEdge("callLlm1", "aggregator")
  .addEdge("callLlm2", "aggregator")
  .addEdge("callLlm3", "aggregator")
  .addEdge("aggregator", "__end__")
  .compile();
```

// 调用
const state = await workflow.invoke({ prompt: "什么是人工智能？" });
console.log(state.finalResult);

// 流式处理
const stream = await workflow.stream({ prompt: "什么是人工智能？" }, {
  streamMode: "updates",

```
for await (const step of stream) {
  console.log(step);
  console.log("\n");
```
}

对于 TypeScript Functional API：

import { task, entrypoint } from "@langchain/langgraph";
// 节点
const callLlm1 = task("callLlm1", async (prompt: string) => {
  // 调用第一个 LLM
```
  const result = await llm.invoke(`从角度 1 回答: ${prompt}`);
  return result.content;
```
});

const callLlm2 = task("callLlm2", async (prompt: string) => {
  // 调用第二个 LLM
```
  const result = await llm.invoke(`从角度 2 回答: ${prompt}`);
  return result.content;
```
});

const callLlm3 = task("callLlm3", async (prompt: string) => {
  // 调用第三个 LLM
```
  const result = await llm.invoke(`从角度 3 回答: ${prompt}`);
  return result.content;
```
});

// 构建工作流
const workflow = entrypoint(
  "parallelWorkflow",
```
  async (prompt: string) => {
    // 并行调用三个 LLM
    const [result1, result2, result3] = await Promise.all([
      callLlm1(prompt),
      callLlm2(prompt),
      callLlm3(prompt)
    ]);
    
    // 聚合结果
    return `结果 1: ${result1}\n\n结果 2: ${result2}\n\n结果 3: ${result3}`;
  }
```
);

// 调用
const result = await workflow.invoke("什么是人工智能？");
console.log(result);

// 流式处理
const stream = await workflow.stream("什么是人工智能？", {
  streamMode: "updates",

```
for await (const step of stream) {
  console.log(step);
  console.log("\n");
```
}
路由

路由工作流允许我们根据输入或中间结果动态选择执行路径。它们适用于需要根据条件分支执行不同操作的情况。

routing.png

让我们构建一个简单的路由工作流，根据问题类型将请求路由到不同的 LLM：

```
from typing import Annotated
from langgraph.graph import StateGraph, START, END
from operator import add
from langgraph.types import StateGraph
from typing_extensions import TypedDict, Literal
from pydantic import BaseModel, Field
```

```
# 定义图状态
class State(TypedDict):
    question: str
    question_type: str
    result: str
```

```
# 定义用于路由的结构化输出模型
class Route(BaseModel):
    question_type: Literal["technical", "philosophical", "creative"] = Field(
        description="问题的类型：technical（技术问题）、philosophical（哲学问题）或 creative（创意问题）"
    )
```

# 使用结构化输出增强 LLM

```
# 节点
def router(state: State):
    """确定问题类型"""
    route = router_llm.invoke(f"确定这个问题的类型: {state['question']}")
    return {"question_type": route.question_type}
```

```
def llm_call_1(state: State):
    """回答技术问题"""
    result = llm.invoke(f"以技术专家的身份回答: {state['question']}")
    return {"result": result.content}
```

```
def llm_call_2(state: State):
    """回答哲学问题"""
    result = llm.invoke(f"以哲学家的身份回答: {state['question']}")
    return {"result": result.content}
```

```
def llm_call_3(state: State):
    """回答创意问题"""
    result = llm.invoke(f"以创意专家的身份回答: {state['question']}")
    return {"result": result.content}
```

```
# 条件边函数，根据问题类型来决定路由
def route_decision(state: State):
    """根据问题类型来决定路由"""
    return state["question_type"]
```

# 构建工作流

# 添加节点
workflow_builder.add_node("llm_call_1", llm_call_1)
workflow_builder.add_node("llm_call_2", llm_call_2)
workflow_builder.add_node("llm_call_3", llm_call_3)

# 添加边来连接节点
workflow_builder.add_conditional_edges(
```
    "router",
    route_decision,
    {  # 由 route_decision 返回的值 : 要访问的下一个节点的名称
        "technical": "llm_call_1",
        "philosophical": "llm_call_2",
        "creative": "llm_call_3"
    }
```
)
workflow_builder.add_edge("llm_call_1", END)
workflow_builder.add_edge("llm_call_2", END)
workflow_builder.add_edge("llm_call_3", END)

# 编译工作流

```
# 显示工作流
from IPython.display import display, Image
```
display(Image(workflow.get_graph().draw_mermaid_png()))

# 调用
print(state["result"])
对于 Functional API：

```
from langgraph.graph import task, entrypoint
from typing_extensions import Literal
from pydantic import BaseModel, Field
```

```
# 定义用于路由的结构化输出模型
class Route(BaseModel):
    question_type: Literal["technical", "philosophical", "creative"] = Field(
        description="问题的类型：technical（技术问题）、philosophical（哲学问题）或 creative（创意问题）"
    )
```

# 使用结构化输出增强 LLM

```
# 节点
@task
def llm_call_1(question: str):
    """回答技术问题"""
    result = llm.invoke(f"以技术专家的身份回答: {question}")
    return result.content
```

```
@task
def llm_call_2(question: str):
    """回答哲学问题"""
    result = llm.invoke(f"以哲学家的身份回答: {question}")
    return result.content
```

```
@task
def llm_call_3(question: str):
    """回答创意问题"""
    result = llm.invoke(f"以创意专家的身份回答: {question}")
    return result.content
```

```
@task
def router(question: str):
    """确定问题类型"""
    route = router_llm.invoke(f"确定这个问题的类型: {question}")
    return route.question_type
```

```
@entrypoint()
def workflow(question: str):
    question_type = router(question).result()
    
    if question_type == "technical":
        result = llm_call_1(question).result()
    elif question_type == "philosophical":
        result = llm_call_2(question).result()
    elif question_type == "creative":
        result = llm_call_3(question).result()
    else:
        result = "无法确定问题类型"
    
    return result
```

# 调用
print(result)
```
# 流式处理
for step in workflow.stream("什么是量子计算？", stream_mode="updates"):
    print(step)
    print("\n")
```

对于 TypeScript：

```
import { Annotation, StateGraph } from "@langchain/langgraph";
import * as z from "zod";
```

// 定义图状态
const StateAnnotation = Annotation.Root({
```
  question: Annotation<string>,
  questionType: Annotation<string>,
  result: Annotation<string>,
```
});

// 定义用于路由的结构化输出模型
const routeSchema = z.object({
```
  questionType: z.enum(["technical", "philosophical", "creative"]).describe(
    "问题的类型：technical（技术问题）、philosophical（哲学问题）或 creative（创意问题）"
  ),
```
});

// 使用结构化输出增强 LLM
const routerLlm = llm.withStructuredOutput(routeSchema);

// 节点
async function router(state: typeof StateAnnotation.State) {
  // 确定问题类型
```
  const route = await routerLlm.invoke(`确定这个问题的类型: ${state.question}`);
  return { questionType: route.questionType };
```
}

async function llmCall1(state: typeof StateAnnotation.State) {
  // 回答技术问题
```
  const result = await llm.invoke(`以技术专家的身份回答: ${state.question}`);
  return { result: result.content };
```
}

async function llmCall2(state: typeof StateAnnotation.State) {
  // 回答哲学问题
```
  const result = await llm.invoke(`以哲学家的身份回答: ${state.question}`);
  return { result: result.content };
```
}

async function llmCall3(state: typeof StateAnnotation.State) {
  // 回答创意问题
```
  const result = await llm.invoke(`以创意专家的身份回答: ${state.question}`);
  return { result: result.content };
```
}

// 条件边函数，根据问题类型来决定路由
function routeDecision(state: typeof StateAnnotation.State) {
  // 根据问题类型来决定路由
  return state.questionType;

// 构建工作流
const workflow = new StateGraph(StateAnnotation)
```
  .addNode("router", router)
  .addNode("llmCall1", llmCall1)
  .addNode("llmCall2", llmCall2)
  .addNode("llmCall3", llmCall3)
  .addEdge("__start__", "router")
  .addConditionalEdges(
    "router",
    routeDecision,
    {
      "technical": "llmCall1",
      "philosophical": "llmCall2",
      "creative": "llmCall3"
    }
  )
  .addEdge("llmCall1", "__end__")
  .addEdge("llmCall2", "__end__")
  .addEdge("llmCall3", "__end__")
  .compile();
```

// 调用
const state = await workflow.invoke({ question: "什么是量子计算？" });
console.log(state.result);

// 流式处理
const stream = await workflow.stream({ question: "什么是量子计算？" }, {
  streamMode: "updates",

```
for await (const step of stream) {
  console.log(step);
  console.log("\n");
```
}

对于 TypeScript Functional API：

```
import { task, entrypoint } from "@langchain/langgraph";
import * as z from "zod";
```

// 定义用于路由的结构化输出模型
const routeSchema = z.object({
```
  questionType: z.enum(["technical", "philosophical", "creative"]).describe(
    "问题的类型：technical（技术问题）、philosophical（哲学问题）或 creative（创意问题）"
  ),
```
});

// 使用结构化输出增强 LLM
const routerLlm = llm.withStructuredOutput(routeSchema);

// 节点
const llmCall1 = task("llmCall1", async (question: string) => {
  // 回答技术问题
```
  const result = await llm.invoke(`以技术专家的身份回答: ${question}`);
  return result.content;
```
});

const llmCall2 = task("llmCall2", async (question: string) => {
  // 回答哲学问题
```
  const result = await llm.invoke(`以哲学家的身份回答: ${question}`);
  return result.content;
```
});

const llmCall3 = task("llmCall3", async (question: string) => {
  // 回答创意问题
```
  const result = await llm.invoke(`以创意专家的身份回答: ${question}`);
  return result.content;
```
});

const llmCallRouter = task("router", async (question: string) => {
  // 确定问题类型
```
  const route = await routerLlm.invoke(`确定这个问题的类型: ${question}`);
  return route.questionType;
```
});

// 构建工作流
const workflow = entrypoint(
  "routingWorkflow",
```
  async (question: string) => {
    const questionType = await llmCallRouter(question);
    
    if (questionType === "technical") {
      return llmCall1(question);
    } else if (questionType === "philosophical") {
      return llmCall2(question);
    } else if (questionType === "creative") {
      return llmCall3(question);
    } else {
      return "无法确定问题类型";
    }
  }
```
);

// 调用
const result = await workflow.invoke("什么是量子计算？");
console.log(result);

// 流式处理
const stream = await workflow.stream("什么是量子计算？", {
  streamMode: "updates",

```
for await (const step of stream) {
  console.log(step);
  console.log("\n");
```
}
协调器-工作者

协调器-工作者工作流使用一个中央协调器来管理多个工作者。协调器负责规划任务并将其分配给工作者，工作者执行任务，然后协调器汇总结果。这种模式适用于需要分解复杂任务的情况。

orchestrator_worker.png

让我们构建一个简单的协调器-工作者工作流，用于生成报告：

```
from typing import Annotated
from langgraph.graph import StateGraph, START, END
from operator import add
from langgraph.types import StateGraph
from typing_extensions import TypedDict
from pydantic import BaseModel, Field
```

```
# 定义用于规划的结构化输出模型
class Section(BaseModel):
    name: str = Field(description="报告部分的名称")
    description: str = Field(description="报告部分的描述")
```

```
class Sections(BaseModel):
    sections: list[Section] = Field(description="报告的各个部分")
```

# 使用结构化输出增强 LLM

```
# 定义图状态
class State(TypedDict):
    topic: str  # 报告主题
    sections: list[Section]  # 报告部分列表
    completed_sections: Annotated[list, operator.add]  # 完成的部分
    final_report: str  # 最终报告
```

```
# 节点
def orchestrator(state: State):
    """协调器，为报告生成计划"""
    
    # 生成查询
    report_sections = planner.invoke(
        [
            SystemMessage(content="为报告生成一个计划。"),
            HumanMessage(content=f"这是报告主题: {state['topic']}"),
        ]
    )
    
    return {"sections": report_sections.sections}
```

```
def llm_call(state: State):
    """工作者，写入报告的一个部分"""
    
    # 生成部分内容
    results = []
    for section in state["sections"]:
        section_content = llm.invoke(
            [
                SystemMessage(
                    content="按照提供的名称和描述撰写报告部分。每个部分不要包含前言。使用 Markdown 格式。"
                ),
                HumanMessage(
                    content=f"这是部分名称: {section.name} 和描述: {section.description}"
                ),
            ]
        )
        results.append(section_content.content)
    
    return {"completed_sections": results}
```

```
def synthesizer(state: State):
    """合成器，从部分内容合成完整报告"""
    
    # 已完成部分的列表
    completed_sections = state["completed_sections"]
    
    # 格式化已完成的部分为字符串，用作最终部分的上下文
    completed_report_sections = "\n\n---\n\n".join(completed_sections)
    
    return {"final_report": completed_report_sections}
```

# 构建工作流

# 添加节点
orchestrator_worker_builder.add_node("llm_call", llm_call)
orchestrator_worker_builder.add_node("synthesizer", synthesizer)

# 添加边来连接节点
orchestrator_worker_builder.add_edge("orchestrator", "llm_call")
orchestrator_worker_builder.add_edge("llm_call", "synthesizer")
orchestrator_worker_builder.add_edge("synthesizer", END)

# 编译工作流

```
# 显示工作流
from IPython.display import display, Image
```
display(Image(orchestrator_worker.get_graph().draw_mermaid_png()))

# 调用

from IPython.display import Markdown

对于 Functional API：

```
from langgraph.graph import task, entrypoint
from pydantic import BaseModel, Field
```

```
# 定义用于规划的结构化输出模型
class Section(BaseModel):
    name: str = Field(description="报告部分的名称")
    description: str = Field(description="报告部分的描述")
```

```
class Sections(BaseModel):
    sections: list[Section] = Field(description="报告的各个部分")
```

# 使用结构化输出增强 LLM

```
# 节点
@task
def orchestrator(topic: str):
    """协调器，为报告生成计划"""
    
    # 生成查询
    report_sections = planner.invoke(
        [
            SystemMessage(content="为报告生成一个计划。"),
            HumanMessage(content=f"这是报告主题: {topic}"),
        ]
    )
    
    return report_sections.sections
```

```
@task
def llm_call(section: Section):
    """工作者，写入报告的一个部分"""
    
    # 生成部分内容
    section_content = llm.invoke(
        [
            SystemMessage(
                content="按照提供的名称和描述撰写报告部分。每个部分不要包含前言。使用 Markdown 格式。"
            ),
            HumanMessage(
                content=f"这是部分名称: {section.name} 和描述: {section.description}"
            ),
        ]
    )
    
    return section_content.content
```

```
@task
def synthesizer(completed_sections: list[str]):
    """合成器，从部分内容合成完整报告"""
    
    # 格式化已完成的部分为字符串，用作最终部分的上下文
    return "\n\n---\n\n".join(completed_sections)
```

```
@entrypoint()
def orchestrator_worker(topic: str):
    sections = orchestrator(topic).result()
    section_futures = [llm_call(section) for section in sections]
    final_report = synthesizer(
        [section_fut.result() for section_fut in section_futures]
    ).result()
    return final_report
```

# 调用
from IPython.display import Markdown

对于 TypeScript：

```
import { Annotation, StateGraph } from "@langchain/langgraph";
import * as z from "zod";
```

// 定义用于规划的结构化输出模型
const sectionSchema = z.object({
```
  name: z.string().describe("报告部分的名称。"),
  description: z.string().describe(
    "报告部分的主要主题和概念的简要概述。"
  ),
```
});

const sectionsSchema = z.object({
  sections: z.array(sectionSchema).describe("报告的各个部分。"),

// 使用结构化输出增强 LLM
const planner = llm.withStructuredOutput(sectionsSchema);

// 定义图状态
const StateAnnotation = Annotation.Root({
```
  topic: Annotation<string>,
  sections: Annotation<z.infer<typeof sectionSchema>[]>,
  completedSections: Annotation<string[]>({
    default: () => [],
    reducer: (a, b) => a.concat(b),
  }),
  finalReport: Annotation<string>,
```
});

// 节点
async function orchestrator(state: typeof StateAnnotation.State) {
  // 生成查询
```
  const reportSections = await planner.invoke([
    { role: "system", content: "为报告生成一个计划。" },
    { role: "user", content: `这是报告主题: ${state.topic}` },
  ]);
  
  return { sections: reportSections.sections };
```
}

async function llmCall(state: typeof StateAnnotation.State) {
  // 生成部分内容
```
  const results = await Promise.all(
    state.sections.map(async (section) => {
      const sectionContent = await llm.invoke([
        {
          role: "system",
          content: "按照提供的名称和描述撰写报告部分。每个部分不要包含前言。使用 Markdown 格式。",
        },
        {
          role: "user",
          content: `这是部分名称: ${section.name} 和描述: ${section.description}`,
        },
      ]);
      return sectionContent.content;
    })
  );
  
  return { completedSections: results };
```
}

async function synthesizer(state: typeof StateAnnotation.State) {
  // 已完成部分的列表
  const completedSections = state.completedSections;
  // 格式化已完成的部分为字符串，用作最终部分的上下文
```
  const completedReportSections = completedSections.join("\n\n---\n\n");
  
  return { finalReport: completedReportSections };
```
}

// 构建工作流
const orchestratorWorker = new StateGraph(StateAnnotation)
```
  .addNode("orchestrator", orchestrator)
  .addNode("llmCall", llmCall)
  .addNode("synthesizer", synthesizer)
  .addEdge("__start__", "orchestrator")
  .addEdge("orchestrator", "llmCall")
  .addEdge("llmCall", "synthesizer")
  .addEdge("synthesizer", "__end__")
  .compile();
```

// 调用
const state = await orchestratorWorker.invoke({ topic: "创建关于 LLM 缩放定律的报告" });
console.log(state.finalReport);

对于 TypeScript Functional API：

```
import * as z from "zod";
import { task, entrypoint } from "@langchain/langgraph";
```

// 定义用于规划的结构化输出模型
const sectionSchema = z.object({
```
  name: z.string().describe("报告部分的名称。"),
  description: z.string().describe(
    "报告部分的主要主题和概念的简要概述。"
  ),
```
});

const sectionsSchema = z.object({
  sections: z.array(sectionSchema).describe("报告的各个部分。"),

// 使用结构化输出增强 LLM
const planner = llm.withStructuredOutput(sectionsSchema);

// 节点
const orchestrator = task("orchestrator", async (topic: string) => {
  // 生成查询
```
  const reportSections = await planner.invoke([
    { role: "system", content: "为报告生成一个计划。" },
    { role: "user", content: `这是报告主题: ${topic}` },
  ]);
  
  return reportSections.sections;
```
});

const llmCall = task("sectionWriter", async (section: z.infer<typeof sectionSchema>) => {
  // 生成部分内容
```
  const result = await llm.invoke([
    {
      role: "system",
      content: "撰写报告部分。",
    },
    {
      role: "user",
      content: `这是部分名称: ${section.name} 和描述: ${section.description}`,
    },
  ]);
  
  return result.content;
```
});

const synthesizer = task("synthesizer", async (completedSections: string[]) => {
  // 从部分内容合成完整报告
  return completedSections.join("\n\n---\n\n");

// 构建工作流
const workflow = entrypoint(
  "orchestratorWorker",
```
  async (topic: string) => {
    const sections = await orchestrator(topic);
    const completedSections = await Promise.all(
      sections.map((section) => llmCall(section))
    );
    return synthesizer(completedSections);
  }
```
);

// 调用
const stream = await workflow.stream("创建关于 LLM 缩放定律的报告", {
  streamMode: "updates",

```
for await (const step of stream) {
  console.log(step);
```
}
在 LangGraph 中创建工作者

协调器-工作者工作流很常见，LangGraph 为此提供了内置支持。Send API 允许您动态创建工作者节点并向它们发送特定输入。每个工作者都有自己的状态，所有工作者的输出都写入一个共享的状态键，该键可被协调器图访问。这使协调器可以访问所有工作者的输出，并将它们合成为最终输出。下面的示例迭代一个部分列表，并使用 Send API 将一个部分发送给每个工作者。

```
from langgraph.types import Send
from typing_extensions import TypedDict
from pydantic import BaseModel, Field
from langgraph.graph import StateGraph, START, END
from langchain.schema import SystemMessage, HumanMessage
from operator import add
from typing import Annotated
```

```
# 定义用于规划的结构化输出模型
class Section(BaseModel):
    name: str = Field(description="报告部分的名称")
    description: str = Field(description="报告部分的描述")
```

```
class Sections(BaseModel):
    sections: list[Section] = Field(description="报告的各个部分")
```

# 使用结构化输出增强 LLM

```
# 图状态
class State(TypedDict):
    topic: str  # 报告主题
    sections: list[Section]  # 报告部分列表
    completed_sections: Annotated[
        list, operator.add
    ]  # 所有工作者并行写入此键
    final_report: str  # 最终报告
```

```
# 工作者状态
class WorkerState(TypedDict):
    section: Section
    completed_sections: Annotated[list, operator.add]
```

```
# 节点
def orchestrator(state: State):
    """协调器，为报告生成计划"""
```

```
    # 生成查询
    report_sections = planner.invoke(
        [
            SystemMessage(content="为报告生成一个计划。"),
            HumanMessage(content=f"这是报告主题: {state['topic']}"),
        ]
    )
```

    return {"sections": report_sections.sections}
```
def llm_call(state: WorkerState):
    """工作者，写入报告的一个部分"""
```

```
    # 生成部分内容
    section = llm.invoke(
        [
            SystemMessage(
                content="按照提供的名称和描述撰写报告部分。每个部分不要包含前言。使用 Markdown 格式。"
            ),
            HumanMessage(
                content=f"这是部分名称: {state['section'].name} 和描述: {state['section'].description}"
            ),
        ]
    )
```

```
    # 将更新后的部分写入已完成部分
    return {"completed_sections": [section.content]}
```

```
def synthesizer(state: State):
    """合成器，从部分内容合成完整报告"""
```

```
    # 已完成部分的列表
    completed_sections = state["completed_sections"]
```

```
    # 格式化已完成的部分为字符串，用作最终部分的上下文
    completed_report_sections = "\n\n---\n\n".join(completed_sections)
```

    return {"final_report": completed_report_sections}
```
# 条件边函数，创建 llm_call 工作者，每个工作者写入报告的一个部分
def assign_workers(state: State):
    """为计划中的每个部分分配一个工作者"""
```

```
    # 通过 Send() API 并行启动部分撰写
    return [Send("llm_call", {"section": s}) for s in state["sections"]]
```

# 构建工作流

# 添加节点
orchestrator_worker_builder.add_node("llm_call", llm_call)
orchestrator_worker_builder.add_node("synthesizer", synthesizer)

# 添加边来连接节点
orchestrator_worker_builder.add_conditional_edges(
    "orchestrator", assign_workers, ["llm_call"]
orchestrator_worker_builder.add_edge("llm_call", "synthesizer")
orchestrator_worker_builder.add_edge("synthesizer", END)

# 编译工作流

```
# 显示工作流
from IPython.display import display, Image
```
display(Image(orchestrator_worker.get_graph().draw_mermaid_png()))

# 调用

from IPython.display import Markdown

对于 TypeScript：

```
import { Annotation, StateGraph, Send } from "@langchain/langgraph";
import * as z from "zod";
```

// 定义用于规划的结构化输出模型
const sectionSchema = z.object({
```
  name: z.string().describe("报告部分的名称。"),
  description: z.string().describe(
    "报告部分的主要主题和概念的简要概述。"
  ),
```
});

const sectionsSchema = z.object({
  sections: z.array(sectionSchema).describe("报告的各个部分。"),

// 使用结构化输出增强 LLM
const planner = llm.withStructuredOutput(sectionsSchema);

// 图状态
const StateAnnotation = Annotation.Root({
```
  topic: Annotation<string>,
  sections: Annotation<z.infer<typeof sectionSchema>[]>,
  completedSections: Annotation<string[]>({
    default: () => [],
    reducer: (a, b) => a.concat(b),
  }),
  finalReport: Annotation<string>,
```
});

// 工作者状态
const WorkerStateAnnotation = Annotation.Root({
```
  section: Annotation<z.infer<typeof sectionSchema>>,
  completedSections: Annotation<string[]>({
    default: () => [],
    reducer: (a, b) => a.concat(b),
  }),
```
});

// 节点
async function orchestrator(state: typeof StateAnnotation.State) {
  // 生成查询
```
  const reportSections = await planner.invoke([
    { role: "system", content: "为报告生成一个计划。" },
    { role: "user", content: `这是报告主题: ${state.topic}` },
  ]);
```

  return { sections: reportSections.sections };

async function llmCall(state: typeof WorkerStateAnnotation.State) {
  // 生成部分内容
```
  const section = await llm.invoke([
    {
      role: "system",
      content: "按照提供的名称和描述撰写报告部分。每个部分不要包含前言。使用 Markdown 格式。",
    },
    {
      role: "user",
      content: `这是部分名称: ${state.section.name} 和描述: ${state.section.description}`,
    },
  ]);
```

  // 将更新后的部分写入已完成部分
  return { completedSections: [section.content] };

async function synthesizer(state: typeof StateAnnotation.State) {
  // 已完成部分的列表
  const completedSections = state.completedSections;
  // 格式化已完成的部分为字符串，用作最终部分的上下文
  const completedReportSections = completedSections.join("\n\n---\n\n");
  return { finalReport: completedReportSections };

// 条件边函数，创建 llmCall 工作者，每个工作者写入报告的一个部分
function assignWorkers(state: typeof StateAnnotation.State) {
```
  // 通过 Send() API 并行启动部分撰写
  return state.sections.map((section) =>
    new Send("llmCall", { section })
  );
```
}

// 构建工作流
const orchestratorWorker = new StateGraph(StateAnnotation)
```
  .addNode("orchestrator", orchestrator)
  .addNode("llmCall", llmCall)
  .addNode("synthesizer", synthesizer)
  .addEdge("__start__", "orchestrator")
  .addConditionalEdges(
    "orchestrator",
    assignWorkers,
    ["llmCall"]
  )
  .addEdge("llmCall", "synthesizer")
  .addEdge("synthesizer", "__end__")
  .compile();
```

// 调用
const state = await orchestratorWorker.invoke({
  topic: "创建关于 LLM 缩放定律的报告"
console.log(state.finalReport);
评估器-优化器

在评估器-优化器工作流中，一个 LLM 调用创建响应，另一个评估该响应。如果评估器或人在环路确定响应需要改进，则提供反馈并重新创建响应。这个循环持续直到生成可接受的响应。

评估器-优化器工作流通常用于任务有特定成功标准但需要迭代才能满足该标准的情况。例如，在两种语言之间翻译文本时，并非总是完美匹配。可能需要几次迭代才能生成在两种语言中具有相同含义的翻译。

evaluator_optimizer.png
```
from typing import Annotated
from langgraph.graph import StateGraph, START, END
from operator import add
from langgraph.types import StateGraph
from typing_extensions import TypedDict, Literal
from pydantic import BaseModel, Field
```

```
# 定义图状态
class State(TypedDict):
    joke: str
    topic: str
    feedback: str
    funny_or_not: str
```

```
# 定义用于评估的结构化输出模型
class Feedback(BaseModel):
    grade: Literal["funny", "not funny"] = Field(
        description="判断笑话是否有趣。",
    )
    feedback: str = Field(
        description="如果笑话不有趣，提供如何改进的反馈。",
    )
```

# 使用结构化输出增强 LLM

```
# 节点
def llm_call_generator(state: State):
    """LLM 生成笑话"""
```

```
    if state.get("feedback"):
        msg = llm.invoke(
            f"写一个关于 {state['topic']} 的笑话，但考虑到反馈: {state['feedback']}"
        )
    else:
        msg = llm.invoke(f"写一个关于 {state['topic']} 的笑话")
    return {"joke": msg.content}
```

```
def llm_call_evaluator(state: State):
    """LLM 评估笑话"""
```

```
    grade = evaluator.invoke(f"评价笑话 {state['joke']}")
    return {"funny_or_not": grade.grade, "feedback": grade.feedback}
```

```
# 条件边函数，根据评估器的反馈决定路由回笑话生成器还是结束
def route_joke(state: State):
    """根据评估器的反馈决定路由回笑话生成器还是结束"""
```

```
    if state["funny_or_not"] == "funny":
        return "Accepted"
    elif state["funny_or_not"] == "not funny":
        return "Rejected + Feedback"
```

# 构建工作流

# 添加节点
optimizer_builder.add_node("llm_call_evaluator", llm_call_evaluator)

# 添加边来连接节点
optimizer_builder.add_edge("llm_call_generator", "llm_call_evaluator")
optimizer_builder.add_conditional_edges(
```
    "llm_call_evaluator",
    route_joke,
    {  # 由 route_joke 返回的值 : 要访问的下一个节点的名称
        "Accepted": END,
        "Rejected + Feedback": "llm_call_generator",
    },
```
)

# 编译工作流

```
# 显示工作流
from IPython.display import display, Image
```
display(Image(optimizer_workflow.get_graph().draw_mermaid_png()))

# 调用
print(state["joke"])
对于 Functional API：

```
from langgraph.graph import task, entrypoint
from typing_extensions import Literal
from pydantic import BaseModel, Field
from typing import Optional
```

```
# 定义用于评估的结构化输出模型
class Feedback(BaseModel):
    grade: Literal["funny", "not funny"] = Field(
        description="判断笑话是否有趣。",
    )
    feedback: str = Field(
        description="如果笑话不有趣，提供如何改进的反馈。",
    )
```

# 使用结构化输出增强 LLM

```
# 节点
@task
def llm_call_generator(topic: str, feedback: Optional[Feedback] = None):
    """LLM 生成笑话"""
    if feedback:
        msg = llm.invoke(
            f"写一个关于 {topic} 的笑话，但考虑到反馈: {feedback.feedback}"
        )
    else:
        msg = llm.invoke(f"写一个关于 {topic} 的笑话")
    return msg.content
```

```
@task
def llm_call_evaluator(joke: str):
    """LLM 评估笑话"""
    feedback = evaluator.invoke(f"评价笑话 {joke}")
    return feedback
```

```
@entrypoint()
def optimizer_workflow(topic: str):
    feedback = None
    while True:
        joke = llm_call_generator(topic, feedback).result()
        feedback = llm_call_evaluator(joke).result()
        if feedback.grade == "funny":
            break
```

    return joke
```
# 调用
for step in optimizer_workflow.stream("猫", stream_mode="updates"):
    print(step)
    print("\n")
```

对于 TypeScript：

```
import * as z from "zod";
import { Annotation, StateGraph } from "@langchain/langgraph";
```

// 定义图状态
const StateAnnotation = Annotation.Root({
```
  joke: Annotation<string>,
  topic: Annotation<string>,
  feedback: Annotation<string>,
  funnyOrNot: Annotation<string>,
```
});

// 定义用于评估的结构化输出模型
const feedbackSchema = z.object({
```
  grade: z.enum(["funny", "not funny"]).describe(
    "判断笑话是否有趣。"
  ),
  feedback: z.string().describe(
    "如果笑话不有趣，提供如何改进的反馈。"
  ),
```
});

// 使用结构化输出增强 LLM
const evaluator = llm.withStructuredOutput(feedbackSchema);

// 节点
async function llmCallGenerator(state: typeof StateAnnotation.State) {
  // LLM 生成笑话
  let msg;
```
  if (state.feedback) {
    msg = await llm.invoke(
      `写一个关于 ${state.topic} 的笑话，但考虑到反馈: ${state.feedback}`
    );
  } else {
    msg = await llm.invoke(`写一个关于 ${state.topic} 的笑话`);
  }
  return { joke: msg.content };
```
}

async function llmCallEvaluator(state: typeof StateAnnotation.State) {
  // LLM 评估笑话
```
  const grade = await evaluator.invoke(`评价笑话 ${state.joke}`);
  return { funnyOrNot: grade.grade, feedback: grade.feedback };
```
}

// 条件边函数，根据评估器的反馈决定路由回笑话生成器还是结束
function routeJoke(state: typeof StateAnnotation.State) {
  // 根据评估器的反馈决定路由回笑话生成器还是结束
```
  if (state.funnyOrNot === "funny") {
    return "Accepted";
  } else if (state.funnyOrNot === "not funny") {
    return "Rejected + Feedback";
  }
```
}

// 构建工作流
const optimizerWorkflow = new StateGraph(StateAnnotation)
```
  .addNode("llmCallGenerator", llmCallGenerator)
  .addNode("llmCallEvaluator", llmCallEvaluator)
  .addEdge("__start__", "llmCallGenerator")
  .addEdge("llmCallGenerator", "llmCallEvaluator")
  .addConditionalEdges(
    "llmCallEvaluator",
    routeJoke,
    {
      // 由 routeJoke 返回的值 : 要访问的下一个节点的名称
      "Accepted": "__end__",
      "Rejected + Feedback": "llmCallGenerator",
    }
  )
  .compile();
```

// 调用
const state = await optimizerWorkflow.invoke({ topic: "猫" });
console.log(state.joke);
```
import * as z from "zod";
import { task, entrypoint } from "@langchain/langgraph";
```

// 定义用于评估的结构化输出模型
const feedbackSchema = z.object({
```
  grade: z.enum(["funny", "not funny"]).describe(
    "判断笑话是否有趣。"
  ),
  feedback: z.string().describe(
    "如果笑话不有趣，提供如何改进的反馈。"
  ),
```
});

// 使用结构化输出增强 LLM
const evaluator = llm.withStructuredOutput(feedbackSchema);

// 节点
const llmCallGenerator = task("jokeGenerator", async (params: {
```
  topic: string;
  feedback?: z.infer<typeof feedbackSchema>;
```
}) => {
  // LLM 生成笑话
```
  const msg = params.feedback
    ? await llm.invoke(
        `写一个关于 ${params.topic} 的笑话，但考虑到反馈: ${params.feedback.feedback}`
      )
    : await llm.invoke(`写一个关于 ${params.topic} 的笑话`);
  return msg.content;
```
});

const llmCallEvaluator = task("jokeEvaluator", async (joke: string) => {
  // LLM 评估笑话
  return evaluator.invoke(`评价笑话 ${joke}`);

// 构建工作流
const workflow = entrypoint(
  "optimizerWorkflow",
```
  async (topic: string) => {
    let feedback: z.infer<typeof feedbackSchema> | undefined;
    let joke: string;
```

```
    while (true) {
      joke = await llmCallGenerator({ topic, feedback });
      feedback = await llmCallEvaluator(joke);
```

```
      if (feedback.grade === "funny") {
        break;
      }
    }
```

```
    return joke;
  }
```
);

// 调用
const stream = await workflow.stream("猫", {
  streamMode: "updates",

```
for await (const step of stream) {
  console.log(step);
  console.log("\n");
```
}
智能体

智能体通常是通过 LLM 使用工具执行操作来实现的。它们在连续的反馈循环中运行，用于问题和解决方案不可预测的情况。智能体比工作流具有更多的自主性，可以决定使用哪些工具以及如何解决问题。您仍然可以定义可用的工具集和智能体行为的指导方针。

agent.png
要开始使用智能体，请参阅[快速入门](/v1/python/langchain/quickstart)或在 LangChain 中了解更多关于[它们如何工作](/v1/python/langchain/agents)的信息。
from langchain.tools import tool

```
# 定义工具
@tool
def multiply(a: int, b: int) -> int:
    """计算 `a` 和 `b` 的乘积。
```

```
    Args:
        a: 第一个整数
        b: 第二个整数
    """
    return a * b
```


```
@tool
def add(a: int, b: int) -> int:
    """计算 `a` 和 `b` 的和。
```

```
    Args:
        a: 第一个整数
        b: 第二个整数
    """
    return a + b
```


```
@tool
def divide(a: int, b: int) -> float:
    """计算 `a` 除以 `b` 的商。
```

```
    Args:
        a: 第一个整数
        b: 第二个整数
    """
    return a / b
```


# 使用工具增强 LLM
tools_by_name = {tool.name: tool for tool in tools}
llm_with_tools = llm.bind_tools(tools)
```
from langgraph.graph import MessagesState
from langchain.messages import SystemMessage, HumanMessage, ToolMessage
from typing_extensions import Literal
```


```
# 节点
def llm_call(state: MessagesState):
```
"""LLM 决定是否调用工具"""

```
    return {
        "messages": [
            llm_with_tools.invoke(
                [
                    SystemMessage(
                        content="你是一个有用的助手，负责对一组输入执行算术运算。"
                    )
                ]
                + state["messages"]
            )
        ]
    }
```


def tool_node(state: dict):

```
    result = []
    for tool_call in state["messages"][-1].tool_calls:
        tool = tools_by_name[tool_call["name"]]
        observation = tool.invoke(tool_call["args"])
        result.append(ToolMessage(content=observation, tool_call_id=tool_call["id"]))
    return {"messages": result}
```


```
# 条件边函数，根据 LLM 是否进行了工具调用来决定路由到工具节点还是结束
def should_continue(state: MessagesState) -> Literal["tool_node", END]:
```
"""根据 LLM 是否进行了工具调用来决定是否继续循环或停止"""

```
    messages = state["messages"]
    last_message = messages[-1]
```

```
    # 如果 LLM 进行了工具调用，则执行操作
    if last_message.tool_calls:
        return "tool_node"
```

```
    # 否则，我们停止（回复用户）
    return END
```


# 构建工作流

# 添加节点
agent_builder.add_node("tool_node", tool_node)

# 添加边来连接节点
agent_builder.add_conditional_edges(
"llm_call",
should_continue,
["tool_node", END]
)
agent_builder.add_edge("tool_node", "llm_call")

# 编译智能体

```
# 显示智能体
from IPython.display import display, Image
```
display(Image(agent.get_graph(xray=True).draw_mermaid_png()))

# 调用
messages = agent.invoke({"messages": messages})
for m in messages["messages"]:
```
from langgraph.graph import add_messages
from langchain.messages import (
    SystemMessage,
    HumanMessage,
    BaseMessage,
    ToolCall,
```
)


```
@task
def call_llm(messages: list[BaseMessage]):
    """LLM 决定是否调用工具"""
    return llm_with_tools.invoke(
        [
            SystemMessage(
                content="你是一个有用的助手，负责对一组输入执行算术运算。"
            )
        ]
        + messages
    )
```


```
@task
def call_tool(tool_call: ToolCall):
    """执行工具调用"""
    tool = tools_by_name[tool_call["name"]]
    return tool.invoke(tool_call)
```


```
@entrypoint()
def agent(messages: list[BaseMessage]):
    llm_response = call_llm(messages).result()
```

```
    while True:
        if not llm_response.tool_calls:
            break
```

```
        # 执行工具
        tool_result_futures = [
            call_tool(tool_call) for tool_call in llm_response.tool_calls
        ]
        tool_results = [fut.result() for fut in tool_result_futures]
        messages = add_messages(messages, [llm_response, *tool_results])
        llm_response = call_llm(messages).result()
```

```
    messages = add_messages(messages, llm_response)
    return messages
```

# 调用
```
for chunk in agent.stream(messages, stream_mode="updates"):
    print(chunk)
    print("\n")
import { tool } from "@langchain/core/tools";
import * as z from "zod";
```

// 定义工具
const multiply = tool(
```
  ({ a, b }) => {
    return a * b;
  },
  {
    name: "multiply",
    description: "计算两个数字的乘积",
    schema: z.object({
      a: z.number().describe("第一个数字"),
      b: z.number().describe("第二个数字"),
    }),
  }
```
);

const add = tool(
```
  ({ a, b }) => {
    return a + b;
  },
  {
    name: "add",
    description: "计算两个数字的和",
    schema: z.object({
      a: z.number().describe("第一个数字"),
      b: z.number().describe("第二个数字"),
    }),
  }
```
);

const divide = tool(
```
  ({ a, b }) => {
    return a / b;
  },
  {
    name: "divide",
    description: "计算两个数字的商",
    schema: z.object({
      a: z.number().describe("第一个数字"),
      b: z.number().describe("第二个数字"),
    }),
  }
```
);

// 使用工具增强 LLM
const tools = [add, multiply, divide];
const toolsByName = Object.fromEntries(tools.map((tool) => [tool.name, tool]));
const llmWithTools = llm.bindTools(tools);
```
import { MessagesAnnotation, StateGraph } from "@langchain/langgraph";
import { ToolNode } from "@langchain/langgraph/prebuilt";
import {
  SystemMessage,
  ToolMessage
```
} from "@langchain/core/messages";

// 节点
async function llmCall(state: typeof MessagesAnnotation.State) {
// LLM 决定是否调用工具
const result = await llmWithTools.invoke([
{
role: "system",
content: "你是一个有用的助手，负责对一组输入执行算术运算。"
},
...state.messages
]);

return {
};
}

const toolNode = new ToolNode(tools);

// 条件边函数，决定路由到工具节点还是结束
function shouldContinue(state: typeof MessagesAnnotation.State) {
const messages = state.messages;
const lastMessage = messages.at(-1);

// 如果 LLM 进行了工具调用，则执行操作
```
if (lastMessage?.tool_calls?.length) {
return "toolNode";
```
}
// 否则，我们停止（回复用户）
return "__end__";

// 构建工作流
const agentBuilder = new StateGraph(MessagesAnnotation)
.addNode("llmCall", llmCall)
.addNode("toolNode", toolNode)
// 添加边来连接节点
.addEdge("__start__", "llmCall")
.addConditionalEdges(
"llmCall",
shouldContinue,
["toolNode", "__end__"]
)
.addEdge("toolNode", "llmCall")
.compile();

// 调用
const messages = [{
role: "user",
content: "计算 3 加 4。"
}];
const result = await agentBuilder.invoke({ messages });
console.log(result.messages);
```
import { task, entrypoint, addMessages } from "@langchain/langgraph";
import { BaseMessageLike, ToolCall } from "@langchain/core/messages";
```

const callLlm = task("llmCall", async (messages: BaseMessageLike[]) => {
  // LLM 决定是否调用工具
```
  return llmWithTools.invoke([
    {
      role: "system",
      content: "你是一个有用的助手，负责对一组输入执行算术运算。"
    },
    ...messages
  ]);
```
});

const callTool = task("toolCall", async (toolCall: ToolCall) => {
  // 执行工具调用
```
  const tool = toolsByName[toolCall.name];
  return tool.invoke(toolCall.args);
```
});

const agent = entrypoint(
  "agent",
```
  async (messages) => {
    let llmResponse = await callLlm(messages);
```

```
    while (true) {
      if (!llmResponse.tool_calls?.length) {
        break;
      }
```

```
      // 执行工具
      const toolResults = await Promise.all(
        llmResponse.tool_calls.map((toolCall) => callTool(toolCall))
      );
```

```
      messages = addMessages(messages, [llmResponse, ...toolResults]);
      llmResponse = await callLlm(messages);
    }
```

```
    messages = addMessages(messages, [llmResponse]);
    return messages;
  }
```
);

// 调用
const messages = [{
```
  role: "user",
  content: "计算 3 加 4。"
```
}];

const stream = await agent.stream([messages], {
  streamMode: "updates",

```
for await (const step of stream) {
  console.log(step);
```
}
