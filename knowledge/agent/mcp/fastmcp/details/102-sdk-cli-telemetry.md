---
title: "SDK: CLI & Telemetry"
source: "https://fastmcp.wiki/zh/python-sdk/fastmcp-cli"
version: "latest"
---

# SDK: CLI & Telemetry

> 原始文档来源：https://fastmcp.wiki/zh/python-sdk/fastmcp-cli (Python SDK API Reference)

---

fastmcp.cli
cli

fastmcp.cli
FastMCP CLI 包。
client

fastmcp.telemetry
函数
get_tracer 
inject_trace_context 
record_span_error 
extract_trace_context 
telemetry

fastmcp.telemetry
FastMCP 的 OpenTelemetry instrumentation。
此模块为 FastMCP 服务端和客户端提供原生 OpenTelemetry 集成。它只使用 opentelemetry-api 包，因此除非用户安装 OpenTelemetry SDK 并配置 exporters，否则 telemetry 不会执行任何操作。
使用 SDK 的示例：
from opentelemetry import trace
from opentelemetry.sdk.trace import TracerProvider
from opentelemetry.sdk.trace.export import ConsoleSpanExporter, SimpleSpanProcessor

# 配置 SDK（由用户负责）
provider = TracerProvider()
provider.add_span_processor(SimpleSpanProcessor(ConsoleSpanExporter()))
trace.set_tracer_provider(provider)

# 现在 FastMCP 会发出 traces
from fastmcp import FastMCP
mcp = FastMCP("my-server")

函数

get_tracer 
get_tracer(version: str | None = None) -> Tracer

获取用于创建 spans 的 FastMCP tracer。
参数：
version: instrumentation 的可选版本字符串
返回：
tracer 实例。如果未配置 SDK，则返回 no-op tracer。

inject_trace_context 
inject_trace_context(meta: dict[str, Any] | None = None) -> dict[str, Any] | None

将当前 trace context 注入 meta dict，以便 MCP 请求传播。
参数：
meta: 要与 trace context 合并的可选现有 meta dict
返回：
一个新 dict，包含原始 meta（如果有）以及 trace context key；如果没有要注入的 trace context 且 meta 为 None，则返回 None

record_span_error 
record_span_error(span: Span, exception: BaseException) -> None

在 span 上记录异常并设置错误状态。

extract_trace_context 
extract_trace_context(meta: dict[str, Any] | None) -> Context

从 MCP 请求 meta dict 中提取 trace context。
如果已经处于有效 trace 中（例如来自 HTTP 传播），则保留现有 trace context，不使用 meta。
参数：
meta: 来自 MCP 请求的 meta dict（ctx.request_context.meta）
返回：
包含提取出的 trace context 的 OpenTelemetry Context；如果未找到 trace context 或已处于 trace 中，则返回当前 context
settings
tools

fastmcp.mcp_config
函数
infer_transport_type_from_url 
update_config_file 
类
StdioMCPServer 
to_transport 
TransformingStdioMCPServer 
RemoteMCPServer 
to_transport 
TransformingRemoteMCPServer 
MCPConfig 
wrap_servers_at_root 
add_server 
from_dict 
to_dict 
write_to_file 
from_file 
CanonicalMCPConfig 
add_server 
mcp_config

fastmcp.mcp_config
规范的 MCP 配置格式。
此模块定义了模型上下文协议 (MCP) 服务器的标准配置格式。 它提供了一个客户端无关的可扩展格式，可以在所有 MCP 实现中使用。
该配置格式支持 stdio 和远程 (HTTP/SSE) 传输，为服务器元数据、 身份验证和执行参数提供全面的字段定义。
配置示例：
{
    "mcpServers": {
        "my-server": {
            "command": "npx",
            "args": ["-y", "@my/mcp-server"],
            "env": {"API_KEY": "secret"},
            "timeout": 30000,
            "description": "My MCP server"
        }
    }
}

函数

infer_transport_type_from_url 
infer_transport_type_from_url(url: str | AnyUrl) -> Literal['http', 'sse']

从给定的 URL 推断适当的传输类型。

update_config_file 
update_config_file(file_path: Path, server_name: str, server_config: CanonicalMCPServerTypes) -> None

从服务器对象更新 MCP 配置文件，保留现有字段。
这用于更新第三方工具的 mcpServer 配置，因此我们不需要担心 在这里转换服务器对象。

类

StdioMCPServer 
用于 stdio 传输的 MCP 服务器配置。
这是使用 stdio 传输的 MCP 服务器的规范配置格式。
方法：

to_transport 
to_transport(self) -> StdioTransport

TransformingStdioMCPServer 
带有工具转换的 Stdio 服务器。

RemoteMCPServer 
用于 HTTP/SSE 传输的 MCP 服务器配置。
这是使用远程传输的 MCP 服务器的规范配置格式。
方法：

to_transport 
to_transport(self) -> StreamableHttpTransport | SSETransport

TransformingRemoteMCPServer 
带有工具转换的远程服务器。

MCPConfig 
符合规范 MCP 配置格式的 MCP 服务器配置对象， 同时添加了额外字段以启用 FastMCP 特定功能，如工具转换 和按标签过滤。
有关严格规范的 MCPConfig，请参阅 CanonicalMCPConfig 类。
方法：

wrap_servers_at_root 
wrap_servers_at_root(cls, values: dict[str, Any]) -> dict[str, Any]

如果不存在“mcpServers”键，但根目录中有服务器配置信息，则对其进行封装。

add_server 
add_server(self, name: str, server: MCPServerTypes) -> None

在配置中添加或更新服务器。

from_dict 
from_dict(cls, config: dict[str, Any]) -> Self

从字典格式解析 MCP 配置。

to_dict 
to_dict(self) -> dict[str, Any]

将 MCPConfig 转换为字典格式，保留所有字段。

write_to_file 
write_to_file(self, file_path: Path) -> None

将配置写入 JSON 文件。

from_file 
from_file(cls, file_path: Path) -> Self

从 JSON 文件加载配置。

CanonicalMCPConfig 
规范的 MCP 配置格式。
这定义了模型上下文协议服务器的标准配置格式。 该格式设计为客户端无关且可为未来用例扩展。
方法：

add_server 
add_server(self, name: str, server: CanonicalMCPServerTypes) -> None

在配置中添加或更新服务器。
exceptions
prompts

