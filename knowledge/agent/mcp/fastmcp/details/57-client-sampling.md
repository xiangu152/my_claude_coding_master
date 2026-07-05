---
title: "LLM 采样"
source: "https://fastmcp.wiki/zh/clients/sampling"
version: "latest"
---

# LLM 采样

> 原始文档来源：https://fastmcp.wiki/zh/clients/sampling

---

处理器模板
处理器参数
内置处理器
OpenAI 处理器
Anthropic 处理器
Google Gemini 处理器
采样能力
工具执行
操作
LLM 采样

处理服务端发起的 LLM 补全请求。

版本
2.0.0
新增
当你需要响应服务端对 LLM 补全的请求时，请使用此功能。
MCP 服务端可以在工具执行期间向客户端请求 LLM 补全。这让服务端可以把 AI 推理委托给客户端，由客户端控制使用哪个 LLM 以及如何发起请求。

处理器模板
from fastmcp import Client
from fastmcp.client.sampling import SamplingMessage, SamplingParams, RequestContext

async def sampling_handler(
    messages: list[SamplingMessage],
    params: SamplingParams,
    context: RequestContext
) -> str:
    """
    处理服务端对 LLM 补全的请求。

    Args:
        messages: 要发送给 LLM 的对话消息
        params: 采样参数（temperature、max_tokens 等）
        context: 带元数据的请求上下文

    Returns:
        由你的 LLM 生成的文本响应
    """
    # 提取消息内容
    conversation = []
    for message in messages:
        content = message.content.text if hasattr(message.content, 'text') else str(message.content)
        conversation.append(f"{message.role}: {content}")

    # 如果提供了系统提示词，则使用它
    system_prompt = params.systemPrompt or "You are a helpful assistant."

    # 在这里集成你的 LLM 服务
    return "基于消息生成的响应"

client = Client(
    "my_mcp_server.py",
    sampling_handler=sampling_handler,
)

处理器参数
SamplingMessage

role
Literal["user", "assistant"]
消息角色

content
TextContent | ImageContent | AudioContent
消息内容。TextContent 具有 .text 属性。
SamplingParams

systemPrompt
str | None
服务端希望使用的可选系统提示词

modelPreferences
ModelPreferences | None
服务端对模型选择的偏好（提示、成本/速度/智能优先级）

temperature
float | None
采样温度

maxTokens
int
要生成的最大 token 数

stopSequences
list[str] | None
采样的停止序列

tools
list[Tool] | None
LLM 在采样期间可使用的工具

toolChoice
ToolChoice | None
工具使用行为（auto、required 或 none）

内置处理器
FastMCP 为 OpenAI、Anthropic 和 Google Gemini API 提供内置处理器，支持完整采样 API，包括工具使用。

OpenAI 处理器
版本
2.11.0
新增
from fastmcp import Client
from fastmcp.client.sampling.handlers.openai import OpenAISamplingHandler

client = Client(
    "my_mcp_server.py",
    sampling_handler=OpenAISamplingHandler(default_model="gpt-4o"),
)

对于兼容 OpenAI 的 API（例如本地模型）：
from openai import AsyncOpenAI

client = Client(
    "my_mcp_server.py",
    sampling_handler=OpenAISamplingHandler(
        default_model="llama-3.1-70b",
        client=AsyncOpenAI(base_url="http://localhost:8000/v1"),
    ),
)

使用 pip install fastmcp[openai] 安装 OpenAI 处理器。

Anthropic 处理器
版本
2.14.1
新增
from fastmcp import Client
from fastmcp.client.sampling.handlers.anthropic import AnthropicSamplingHandler

client = Client(
    "my_mcp_server.py",
    sampling_handler=AnthropicSamplingHandler(default_model="claude-sonnet-4-5"),
)

使用 pip install fastmcp[anthropic] 安装 Anthropic 处理器。

Google Gemini 处理器
版本
3.1.0
新增
from fastmcp import Client
from fastmcp.client.sampling.handlers.google_genai import GoogleGenaiSamplingHandler

client = Client(
    "my_mcp_server.py",
    sampling_handler=GoogleGenaiSamplingHandler(default_model="gemini-2.0-flash"),
)

使用 pip install fastmcp[gemini] 安装 Google Gemini 处理器。

采样能力
当你提供 sampling_handler 时，FastMCP 会自动向服务端声明完整采样能力，包括工具支持。若要为更简单的处理器禁用工具支持：
from mcp.types import SamplingCapability

client = Client(
    "my_mcp_server.py",
    sampling_handler=basic_handler,
    sampling_capabilities=SamplingCapability(),  # 无工具支持
)

工具执行
工具执行发生在服务端。客户端的职责是将工具传给 LLM，并返回 LLM 的响应（其中可能包含工具使用请求）。随后服务端执行这些工具，并可能携带工具结果发送后续采样请求。
要实现自定义采样处理器，请参考处理器源码。
获取提示词

用户征询

x

