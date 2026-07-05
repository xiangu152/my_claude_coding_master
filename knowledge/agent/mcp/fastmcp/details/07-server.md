---
title: "FastMCP 服务端"
source: "https://fastmcp.wiki/zh/servers/server"
version: "latest"
---

# FastMCP 服务端

> 原始文档来源：https://fastmcp.wiki/zh/servers/server

---

创建服务端
组件
运行服务端
配置参考
身份
组合
行为
处理器和存储
基于标签的过滤
自定义路由
服务端
FastMCP 服务端

用于构建 MCP 应用的核心 FastMCP 服务端类

FastMCP 类是每个 FastMCP 应用的核心。它是工具、资源和提示词的容器，负责管理与 MCP 客户端的通信，并编排整个服务端生命周期。

创建服务端
最简单的 FastMCP 服务端只需要一个名称。其他设置都有合理默认值。
from fastmcp import FastMCP

mcp = FastMCP("MyServer")

Instructions 可以帮助客户端（以及其背后的 LLM）理解服务端的用途，以及如何有效使用它。
mcp = FastMCP(
    "DataAnalysis",
    instructions="Provides tools for analyzing numerical datasets. Start with get_summary() for an overview.",
)

组件
FastMCP 服务端向客户端暴露三类组件，每类组件在 MCP 协议中承担不同角色。
工具是客户端调用的函数，用于执行操作或访问外部系统。
@mcp.tool
def multiply(a: float, b: float) -> float:
    """将两个数字相乘。"""
    return a * b

资源暴露客户端可以读取的数据，是被动数据源，而不是可调用函数。
@mcp.resource("data://config")
def get_config() -> dict:
    return {"theme": "dark", "version": "1.0"}

提示词是可复用的消息模板，用于引导 LLM 交互。
@mcp.prompt
def analyze_data(data_points: list[float]) -> str:
    formatted_data = ", ".join(str(point) for point in data_points)
    return f"Please analyze these data points: {formatted_data}"

每种组件类型都有详细文档：工具、资源（包括资源模板）和提示词。

运行服务端
调用 mcp.run() 即可启动服务端。if __name__ guard 可以确保与那些将服务端作为子进程启动的 MCP 客户端兼容。
from fastmcp import FastMCP

mcp = FastMCP("MyServer")

@mcp.tool
def greet(name: str) -> str:
    """按名称问候用户。"""
    return f"Hello, {name}!"

if __name__ == "__main__":
    mcp.run()

FastMCP 支持多种传输：
STDIO（默认）：用于本地集成和 CLI 工具
HTTP：用于采用 Streamable HTTP 协议的 Web 服务
SSE：旧版 Web 传输（已弃用）
# 使用 HTTP 传输运行
mcp.run(transport="http", host="127.0.0.1", port=9000)

也可以使用 FastMCP CLI 运行服务端。有关传输和部署的详细信息，请参阅运行服务端。

配置参考
FastMCP 构造函数接受的参数分为四类：身份、组合、行为和处理器。

身份
这些参数控制服务端如何向客户端展示自己。

name
str默认值:"FastMCP"
服务端的人类可读名称，会显示在客户端应用和日志中

instructions
str | None
描述如何与此服务端交互。客户端会展示这些 instructions，帮助 LLM 理解服务端用途和可用功能

version
str | None
服务端的版本字符串。如果未提供，默认使用 FastMCP 库版本

website_url
str | None
版本
2.13.0
新增
包含服务端更多信息的网站 URL。会显示在客户端应用中

icons
list[Icon] | None
版本
2.13.0
新增
服务端图标表示列表。详情请参阅图标

experimental_capabilities
dict[str, dict[str, Any]] | None
版本
3.2.5
新增
在 MCP initialize 响应中声明的任意实验性 capabilities。可用于声明跨服务端互操作约定，或遵循 MCP spec experimental 字段的草案扩展。key 是 capability 名称；value 是自由形式 dict。FastMCP 内置派生 capabilities（tools、resources 等）不受影响；这只会填充 capabilities.experimental

组合
这些参数控制服务端由什么构成：组件、中间件、providers 和生命周期。

tools
list[Tool | Callable] | None
要注册到服务端的工具。当你需要以编程方式添加工具时，它是 @mcp.tool 装饰器的替代方案

auth
OAuthProvider | TokenVerifier | None
用于保护基于 HTTP 的传输的身份认证 provider。配置请参阅身份认证

middleware
list[Middleware] | None
中间件会拦截并转换流经服务端的每条 MCP 消息，包括双向的 requests、responses 和 notifications。适用于日志、错误处理、限流等横切关注点

providers
list[Provider] | None
Providers 用于动态提供工具、资源和提示词。Providers 会在请求时被查询，因此可以从数据库、API 或其他外部来源提供组件

transforms
list[Transform] | None
版本
3.1.0
新增
应用于所有组件的服务端级 transforms。Transforms 会修改工具、资源和提示词呈现给客户端的方式；例如，搜索 transforms 可以用按需发现替代大型目录

lifespan
Lifespan | AsyncContextManager | None
服务端启动和停止时运行的服务端级 setup 与 teardown 逻辑。有关可组合 lifespans，请参阅 Lifespans

行为
这些参数用于调节服务端处理请求和与客户端通信的方式。

on_duplicate
Literal["warn", "error", "replace", "ignore"]默认值:"warn"
如何处理重复的组件注册

strict_input_validation
bool默认值:"False"
版本
2.13.0
新增
当为 False（默认）时，FastMCP 使用 Pydantic 的灵活校验，会强制转换兼容输入（例如 int 参数的 "10" → 10）。当为 True 时，会在调用函数前根据精确 JSON Schema 校验输入，并拒绝类型不匹配。详情请参阅输入校验模式

mask_error_details
bool | None
当为 True 时，会用通用消息替换工具/资源响应中的内部错误细节，避免向客户端泄露实现细节。默认使用 FASTMCP_MASK_ERROR_DETAILS 环境变量

list_page_size
int | None默认值:"None"
版本
3.0.0
新增
list 操作（tools/list、resources/list 等）每页的最大条目数。当为 None 时，所有结果会在单个响应中返回。详情请参阅分页

tasks
bool | None默认值:"False"
启用后台任务支持。当为 True 时，工具和资源可以返回 CreateTaskResult，以异步方式运行工作，并由客户端轮询结果

client_log_level
LoggingLevel | None
版本
3.2.0
新增
通过 context.log() 发送给 MCP 客户端的默认最低日志级别。设置后，低于此级别的消息会被抑制。单个客户端可以使用 MCP logging/setLevel 请求按会话覆盖此设置。可选值包括 "debug"、"info"、"notice"、"warning"、"error"、"critical"、"alert" 或 "emergency"

dereference_schemas
bool默认值:"True"
自动解引用由复杂 Pydantic 模型生成的 JSON schemas 中的 $ref 指针。大多数客户端需要不含 $ref 的扁平 schemas，因此通常应保持启用

处理器和存储
这些参数为 MCP capabilities 提供自定义处理器，并为会话状态提供持久化存储。

sampling_handler
SamplingHandler | None
MCP 采样请求（服务端发起的 LLM 调用）的自定义处理器。详情请参阅采样

sampling_handler_behavior
Literal["always", "fallback"] | None默认值:"fallback"
当为 "fallback" 时，只有不存在工具专属处理器时才使用采样处理器。当为 "always" 时，所有采样请求都会使用此处理器

session_state_store
AsyncKeyValue | None
用于会话状态的持久化 key-value store，可跨请求保留状态。默认使用内存中 store。若需要跨服务端重启持久化，请提供自定义实现

基于标签的过滤
版本
2.8.0
新增
标签可以帮助你对组件分类，并选择性地暴露它们。这对于按环境或用户类型创建不同服务端视图很有用。
@mcp.tool(tags={"public", "utility"})
def public_tool() -> str:
    return "This tool is public"

@mcp.tool(tags={"internal", "admin"})
def admin_tool() -> str:
    return "This tool is for admins only"

过滤逻辑如下：
使用 only=True 启用：切换到 allowlist 模式，只暴露至少包含一个匹配标签的组件
禁用：隐藏包含任意匹配标签的组件
优先级：后续调用会覆盖先前调用，因此可以在 enable 之后调用 disable，从 allowlist 中排除组件
要确保某个组件永远不会被暴露，可以在组件自身设置 enabled=False。详情请参阅对应组件的文档。
# 只暴露带有 "public" 标签的组件
mcp = FastMCP()
mcp.enable(tags={"public"}, only=True)

# 隐藏带有 "internal" 或 "deprecated" 标签的组件
mcp = FastMCP()
mcp.disable(tags={"internal", "deprecated"})

# 组合使用：显示 admin 工具，但隐藏 deprecated 工具
mcp = FastMCP()
mcp.enable(tags={"admin"}, only=True).disable(tags={"deprecated"})

此过滤适用于所有组件类型（工具、资源、资源模板和提示词），并同时影响列表展示和访问。

自定义路由
使用 HTTP 传输运行时，可以通过 @custom_route 装饰器在 MCP endpoint 旁添加自定义 Web 路由。
from fastmcp import FastMCP
from starlette.requests import Request
from starlette.responses import PlainTextResponse

mcp = FastMCP("MyServer")

@mcp.custom_route("/health", methods=["GET"])
async def health_check(request: Request) -> PlainTextResponse:
    return PlainTextResponse("OK")

if __name__ == "__main__":
    mcp.run(transport="http")  # 健康检查位于 http://localhost:8000/health

自定义路由适用于健康检查、状态 endpoints 和简单 webhooks。对于更复杂的 Web 应用，可以考虑将 MCP 服务端挂载到 FastAPI 或 Starlette app 中。
快速开始

工具

x

