---
title: "消息与事件"
source: "https://docs.agentscope.io/versions/2.0.3/zh/building-blocks/message-and-event"
version: "2.0.3"
language: "zh"
---

# 消息与事件

> 原始文档来源：https://docs.agentscope.io/versions/2.0.3/zh/building-blocks/message-and-event
> AgentScope 2.0.3 中文文档

---
消息
结构
内容块
创建消息
访问内容
事件
事件生命周期
事件类型
从事件流重建消息
TypeScript 支持
示例：流式界面

智能体通信，与前端流式数据传输

消息（Message）与事件（Event）是 AgentScope 中两种基础数据结构。
消息 — 智能体间通信与上下文持久化的基本单元。
事件 — 前后端交互与流式传输的基本单元，支持人工介入（Human-in-the-loop）场景。

消息
AgentScope 中Msg类的一个实例容纳一次完整的对话信息——一次用户输入或一次完整的智能体回复，信息以不同类型的内容块（Block）进行组织。
智能体运行一次reply产生一个完整的Msg实例，包含多轮的思考，工具调用，运行结果等所有信息。
前端渲染时，一个Msg实例即对应渲染成一个完整的消息气泡。

结构
Msg 类的核心字段如下：
| 字段 | 类型 | 说明 |
| --- | --- | --- |
| id | str | 唯一消息标识符 |
| name | str | 发送方名称 |
| role | "user" | "assistant" | "system" | 发送方角色 |
| content | list[ContentBlock] | 有序内容块列表 |
| metadata | dict | 任意键值元数据 |
| created_at | str | 创建时间（ISO 8601） |
| finished_at | str | None | 消息完成时间（ISO 8601） |
| usage | Usage | 次元用量统计（仅 assistant 消息） |

内容块
消息内容由类型化的块组成，每种块代表一类独立信息：
块类型	说明
TextBlock	纯文本内容
DataBlock	二进制数据（图片、音频、视频等），可以是 base64 或 URL
ThinkingBlock	模型推理过程（思维链）
ToolCallBlock	工具调用，包含名称、输入和状态
ToolResultBlock	工具执行结果
HintBlock	提示信息（例如调度任务触发、团队消息、后台工具结果），支持多模态数据，使用source标识提示来源。
角色约束在构造时强制执行：
msg.role=="user"的消息只能包含TextBlock和DataBlock；
msg.role=="system"的消息只能包含TextBlock；
msg.role=="assistant"的消息可包含所有块类型。
这些数据块承载不同的数据信息，其详细字段说明如下：

TextBlock

文本数据

ThinkingBlock

模型的思考过程

DataBlock

多模态数据（如图片、音频、视频等）

HintBlock

用于引导 LLM 的提示信息

ToolCallBlock

工具调用的数据和状态

ToolResultBlock

工具执行结果的数据和状态


创建消息
AgentScope 提供三个快捷方法来构建Msg对象，以避免重复的设置role参数，并支持从字符串构建TextBlock：
工厂函数	角色
UserMsg(name, content)	user
AssistantMsg(name, content)	assistant
SystemMsg(name, content)	system
当content参数为字符串时，会自动包装为TextBlock。
创建文本消息
创建多模态消息
创建工具调用消息
from agentscope.message import UserMsg, SystemMsg, AssistantMsg

# 用户消息
user_msg = UserMsg(
	name="user",
	content="这张图片里有什么？"
)

# 系统消息，仅用于系统提示（System prompt）
system_msg = SystemMsg(
	name="system",
	content="你是一个名为 Friday 的 AI 助手。"
)

# 助手消息
assistant_msg = AssistantMsg(
	name="Friday",
	content="你好，有什么我可以帮你的吗？"
)


访问内容
Msg 提供了一组辅助方法用于提取特定块类型：
方法	返回值
get_text_content(separator="\n")	返回所有 TextBlock 的拼接文本，或 None
get_content_blocks(block_type)	按类型过滤后的块列表
has_content_blocks(block_type)	若存在指定类型的块则返回 True
# 获取所有文本内容
text = msg.get_text_content()

# 获取所有工具调用
tool_calls = msg.get_content_blocks("tool_call")

# 检查消息是否包含工具结果
if msg.has_content_blocks("tool_result"):
    ...


事件
事件是消息的流式传输单元，即一个序列的事件可以组成一个完整的消息。
智能体类Agent在运行reply_stream的过程中，会产生一系列AgentEvent对象，表示增量的思考、文本回复、工具调用和工具调用结果。前端可通过订阅事件流来实现消息的实时渲染。

事件生命周期
事件的reply_id标识它所属的消息，block_id或tool_call_id标识它所属的内容块。 事件的产生遵循 start → delta → end 模式：
Agent
Client
Agent
Client
推理阶段
TextBlock (block_id)
DataBlock (block_id)
ToolCallBlock (tool_call_id)
执行阶段
ToolResultBlock (tool_call_id)
ReplyStartEvent
ModelCallStartEvent
TextBlockStartEvent
TextBlockDeltaEvent (×N)
TextBlockEndEvent
DataBlockStartEvent
DataBlockDeltaEvent (×N)
DataBlockEndEvent
ToolCallStartEvent
ToolCallDeltaEvent (×N)
ToolCallEndEvent
ModelCallEndEvent
ToolResultStartEvent
ToolResultTextDeltaEvent (×N)
ToolResultDataDeltaEvent (×N)
ToolResultEndEvent
ReplyEndEvent
同一次reply_stream的调用中所有事件共享相同的 reply_id。在回复内部，用 block_id 关联文本/思考/数据块事件，用 tool_call_id 关联工具调用和工具结果事件。

事件类型
所有事件继承自 EventBase，提供以下公共字段：
| 字段 | 类型 | 说明 |
| --- | --- | --- |
| id | str | 唯一事件标识符 |
| created_at | str | ISO 8601 时间戳 |
事件按类别分组如下。除特别说明外，每个事件还携带 reply_id 字段，关联到正在构建的消息。

生命周期事件

文本流式事件

思考流式事件

数据流式事件

工具调用流式事件

工具结果流式事件

模型调用事件

人工介入事件

一次性事件


从事件流重建消息
事件与消息并非相互独立，而是同一数据的两种视图。reply_stream 产出的每个事件都可以通过 append_event() 追加数据到 Msg 上，从而增量地重建完整消息。这保证了最终消息状态可以仅凭事件流完整还原。
from agentscope.message import Msg, AssistantMsg

msg = None

async for event in agent.reply_stream(user_msg):
```python
    if isinstance(event, ReplyStartEvent):
        # 回复开始时创建新消息
        msg = AssistantMsg(name=event.name, content=[], id=event.reply_id)
    else:
        # 其他事件追加到消息，逐步还原状态
        msg.append_event(event)
```

append_event 方法处理所有事件类型：
事件类型	对消息的影响
ReplyEndEvent	设置 finished_at 时间戳
TextBlockStartEvent	追加新的空 TextBlock
TextBlockDeltaEvent	将 delta 拼接到对应块的文本
DataBlockStartEvent	追加新的空 DataBlock
DataBlockDeltaEvent	将 data 拼接到对应块的 base64 内容
ThinkingBlockStartEvent	追加新的空 ThinkingBlock
ThinkingBlockDeltaEvent	将 delta 拼接到对应块的思考文本
ToolCallStartEvent	追加新的空参数 ToolCallBlock
ToolCallDeltaEvent	将 delta 拼接到工具调用的参数
ToolResultStartEvent	追加新的空输出 ToolResultBlock
ToolResultTextDeltaEvent	将文本追加到工具结果的输出
ToolResultDataDeltaEvent	将二进制数据块追加到工具结果的输出
ToolResultEndEvent	设置工具结果的最终 state
HintBlockEvent	将 HintBlock 追加到 content（携带事件的 hint 与 source），从而持久化并可重放该提示
RequireUserConfirmEvent	将对应工具调用的状态更新为 ASKING
ExternalExecutionResultEvent	将 ToolResultBlock 追加到消息内容
这种设计让部署更加灵活：后端可以通过 SSE 将事件流推送到前端，前端重建消息并进行渲染。即使连接中断，从任意检查点重放事件序列也能精确恢复消息状态。

TypeScript 支持
AgentScope 提供 TypeScript 版本的消息和事件元语，前端可以使用相同的 appendEvent API 从事件流重建消息。
安装 TS 版本的 AgentScope：
pnpm install @agentscope-ai/agentscope

前端接收消息并重建消息的示例：
从事件流重建消息
import { Msg, AssistantMsg, EventType } from "@agentscope-ai/agentscope/message";

let msg: Msg | null = null;
for await (const event of stream) {
```python
    if (event.type === EventType.REPLY_START) {
        msg = new AssistantMsg({
			name: event.name,
			content: [],
			id: event.reply_id
		});
    } else {
        msg?.appendEvent(event);
    }
```
}


示例：流式界面
以终端打印为例，展示如何在前端接收事件流并实时渲染：
```python
from agentscope.message import AssistantMsg, UserMsg
from agentscope.event import (
    ReplyStartEvent,
    TextBlockDeltaEvent,
    ToolCallStartEvent,
    ToolResultEndEvent,
    ReplyEndEvent,
```
)

msg = None
async for event in agent.reply_stream(UserMsg("user", "帮我修复这个 bug")):
```python
    if isinstance(event, ReplyStartEvent):
        msg = AssistantMsg(name=event.name, content=[], id=event.reply_id)
```

```python
    elif isinstance(event, TextBlockDeltaEvent):
        print(event.delta, end="", flush=True)
```

```python
    elif isinstance(event, ToolCallStartEvent):
        print(f"\n[正在调用 {event.tool_call_name}...]")
```

```python
    elif isinstance(event, ToolResultEndEvent):
        print(f"[工具执行完成：{event.state}]")
```

```python
    elif isinstance(event, ReplyEndEvent):
        print("\n[完成]")
```

```python
    # 始终将事件追加到消息中
    if msg is not None:
        msg.append_event(event)
```

# msg 现在包含完整的回复内容


智能体如何在 ReAct 循环中产出事件和消息
上下文
消息如何存储、压缩和卸载
更新日志
