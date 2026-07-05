---
title: "SDK: Utilities (Inspect, Components, Parsing)"
source: "https://fastmcp.wiki/zh/python-sdk/fastmcp-utilities-inspect"
version: "latest"
---

# SDK: Utilities (Inspect, Components, Parsing)

> 原始文档来源：https://fastmcp.wiki/zh/python-sdk/fastmcp-utilities-inspect (Python SDK API Reference)

---

fastmcp.utilities.inspect
Functions
inspect_fastmcp_v2 
inspect_fastmcp_v1 
inspect_fastmcp 
format_fastmcp_info 
format_mcp_info 
format_info 
Classes
ToolInfo 
PromptInfo 
ResourceInfo 
TemplateInfo 
FastMCPInfo 
InspectFormat 
fastmcp.utilities
inspect

fastmcp.utilities.inspect
用于检查 FastMCP 实例的工具。

Functions

inspect_fastmcp_v2 
inspect_fastmcp_v2(mcp: FastMCP[Any]) -> FastMCPInfo

从 FastMCP v2.x 实例提取信息。
参数：
mcp：要检查的 FastMCP v2.x 实例
返回：
包含提取信息的 FastMCPInfo dataclass

inspect_fastmcp_v1 
inspect_fastmcp_v1(mcp: FastMCP1x) -> FastMCPInfo

使用 Client 从 FastMCP v1.x 实例提取信息。
参数：
mcp：要检查的 FastMCP v1.x 实例
返回：
包含提取信息的 FastMCPInfo dataclass

inspect_fastmcp 
inspect_fastmcp(mcp: FastMCP[Any] | FastMCP1x) -> FastMCPInfo

将 FastMCP 实例中的信息提取到 dataclass 中。
此函数会自动检测实例是 FastMCP v1.x 还是 v2.x，并使用适当的提取方法。
参数：
mcp：要检查的 FastMCP 实例（v1.x 或 v2.x）
返回：
包含提取信息的 FastMCPInfo dataclass

format_fastmcp_info 
format_fastmcp_info(info: FastMCPInfo) -> bytes

将 FastMCPInfo 格式化为 FastMCP 专用 JSON。
其中包含 FastMCP 专用字段，例如 tags、enabled、annotations 等。

format_mcp_info 
format_mcp_info(mcp: FastMCP[Any] | FastMCP1x) -> bytes

将服务端信息格式化为标准 MCP 协议 JSON。
使用 Client 获取带 camelCase 字段的标准 MCP 协议格式。顶层包含版本元数据。

format_info 
format_info(mcp: FastMCP[Any] | FastMCP1x, format: InspectFormat | Literal['fastmcp', 'mcp'], info: FastMCPInfo | None = None) -> bytes

根据指定格式格式化服务端信息。
参数：
mcp：FastMCP 实例
format：输出格式（“fastmcp” 或 “mcp”）
info：预先提取的 FastMCPInfo（可选；未提供时会自动提取）
返回：
请求格式的 JSON 字节

Classes

ToolInfo 
工具相关信息。

PromptInfo 
提示词相关信息。

ResourceInfo 
资源相关信息。

TemplateInfo 
资源模板相关信息。

FastMCPInfo 
从 FastMCP 实例提取的信息。

InspectFormat 
inspect 命令的输出格式。
http
json_schema

fastmcp.utilities.components
Functions
get_fastmcp_metadata 
Classes
FastMCPMeta 
FastMCPComponent 
make_key 
key 
get_meta 
enable 
disable 
copy 
register_with_docket 
add_to_docket 
get_span_attributes 
fastmcp.utilities
components

fastmcp.utilities.components

Functions

get_fastmcp_metadata 
get_fastmcp_metadata(meta: dict[str, Any] | None) -> FastMCPMeta

从组件的 meta 字典中提取 FastMCP 元数据。
同时处理当前的 fastmcp 命名空间和旧版 _fastmcp 命名空间，以兼容较旧的 FastMCP 服务端。

Classes

FastMCPMeta 

FastMCPComponent 
FastMCP 工具、提示词、资源和资源模板的基类。
Methods:

make_key 
make_key(cls, identifier: str) -> str

为此组件类型构造查找 key。
参数：
identifier：原始标识符（工具/提示词为名称，资源为 uri）
返回：
带前缀的 key，例如 “tool:name” 或 “resource:uri”

key 
key(self) -> str

此组件的全局唯一查找 key。
格式：”:@” 或 ”:@“，例如 “tool:my_tool@v2”、“tool:my_tool@”、“resource:file://x.txt@”
@ 后缀始终存在，以便对 key 进行无歧义解析（URI 可能包含 @ 字符，因此始终包含该分隔符）。
子类应覆盖此项以使用其特定标识符。基础实现使用 name。
对于任何跨组件身份工作（去重、分组、冲突检测、查找表），应优先使用 .key，而不是临时拼接 name or uri or uri_template 逻辑。它会编码类型、标识符和版本，因此同一组件的不同变体不会错误冲突，跨类型标识符（例如工具和资源都名为 “foo”）也不会冲突。

get_meta 
get_meta(self) -> dict[str, Any]

获取组件的 meta 信息。
返回的字典始终包含 fastmcp key，其中包含：
tags：排序后的组件标签列表
version：组件版本（仅在设置时包含）
内部 key（以 _ 为前缀）会从 fastmcp 命名空间中移除。

enable 
enable(self) -> None

已在 3.0 中移除。请改用 server.enable(keys=[…])。

disable 
disable(self) -> None

已在 3.0 中移除。请改用 server.disable(keys=[…])。

copy 
copy(self) -> Self

创建组件副本。

register_with_docket 
register_with_docket(self, docket: Docket) -> None

将此组件注册到 docket，以便后台执行。
如果 task_config.mode 为 “forbidden”，则不执行任何操作。子类会覆盖此方法，以注册其可调用对象（self.run、self.read、self.render 或 self.fn）。

add_to_docket 
add_to_docket(self, docket: Docket, *args: Any, **kwargs: Any) -> Execution

通过 docket 调度此组件进行后台执行。
子类会覆盖此方法以处理各自的调用约定：
Tool: add_to_docket(docket, arguments: dict, **kwargs)
Resource: add_to_docket(docket, **kwargs)
ResourceTemplate: add_to_docket(docket, params: dict, **kwargs)
Prompt: add_to_docket(docket, arguments: dict | None, **kwargs)
**kwargs 会透传给 docket.add()（例如 key=task_key）。

get_span_attributes 
get_span_attributes(self) -> dict[str, Any]

返回用于遥测的 span 属性。
子类应调用 super()，并合并其特定属性。
cli
docstring_parsing

fastmcp.utilities.docstring_parsing
函数
parse_docstring 
类
ParsedDocstring 
fastmcp.utilities
docstring_parsing

fastmcp.utilities.docstring_parsing
从函数 docstring 中提取描述。
使用 griffelib 解析 Google、NumPy 和 Sphinx 风格的 docstring。接口有意保持很窄：一个返回 ParsedDocstring 的函数，这样替换实现时无需改动调用方。

函数

parse_docstring 
parse_docstring(fn: Callable[..., Any]) -> ParsedDocstring

将函数的 docstring 解析为摘要和参数描述。
按顺序尝试 Google、NumPy 和 Sphinx 解析器，并使用第一个成功提取参数描述的解析器。如果都无法提取，则把完整 docstring 作为描述返回，且不包含参数描述。

类

ParsedDocstring 
从 docstring 中提取出的描述和逐参数描述。
components
exceptions

fastmcp.utilities.exceptions
函数
iter_exc 
get_catch_handlers 
fastmcp.utilities
exceptions

fastmcp.utilities.exceptions

函数

iter_exc 
iter_exc(group: BaseExceptionGroup)

get_catch_handlers 
get_catch_handlers() -> Mapping[type[BaseException] | Iterable[type[BaseException]], Callable[[BaseExceptionGroup[Any]], Any]]

docstring_parsing
http

fastmcp.utilities.async_utils
函数
is_coroutine_function 
call_sync_fn_in_threadpool 
gather 
fastmcp.utilities
async_utils

fastmcp.utilities.async_utils
FastMCP 的异步实用工具。

函数

is_coroutine_function 
is_coroutine_function(fn: Any) -> bool

检查可调用对象是否为协程函数，并展开 functools.partial。
在 Python < 3.12 中，对于包装异步函数的 functools.partial 对象，inspect.iscoroutinefunction 会返回 False。此辅助函数会在检查前展开所有 partial 层。

call_sync_fn_in_threadpool 
call_sync_fn_in_threadpool(fn: Callable[..., Any], *args: Any, **kwargs: Any) -> Any

在线程池中调用同步函数，以避免阻塞事件循环。
使用 anyio.to_thread.run_sync，它会正确传播 contextvars，因此对于依赖上下文的函数（例如依赖注入）是安全的。

gather 
gather(*awaitables: Awaitable[T]) -> list[T] | list[T | BaseException]

并发运行 awaitable，并按顺序返回结果。
使用 anyio TaskGroup 实现结构化并发。
参数：
*awaitables: 要并发运行的 awaitable
return_exceptions: 如果为 True，异常会作为结果返回。如果为 False，第一个异常会取消所有任务并抛出。
返回：
与输入 awaitable 顺序相同的结果列表。
__init__
auth

fastmcp.utilities.lifespan
函数
combine_lifespans 
fastmcp.utilities
lifespan

fastmcp.utilities.lifespan
用于组合异步上下文管理器 lifespan 的实用工具。

函数

combine_lifespans 
combine_lifespans(*lifespans: Callable[[AppT], AbstractAsyncContextManager[Mapping[str, Any] | None]]) -> Callable[[AppT], AbstractAsyncContextManager[dict[str, Any]]]

将多个 lifespan 组合为单个 lifespan。
当你把 FastMCP 挂载到 FastAPI 中，并且需要同时运行应用的 lifespan 和 MCP 服务端的 lifespan 时，这很有用。
同时支持 FastAPI 风格的 lifespan（yield None）和 FastMCP 风格的 lifespan（yield dict）。结果会被合并；发生键冲突时，后面的 lifespan 会覆盖前面的值。
lifespan 会按顺序进入，并按相反顺序退出（LIFO）。
参数：
*lifespans: 要组合的 lifespan 上下文管理器工厂。
返回：
组合后的 lifespan 上下文管理器工厂。
json_schema_type
logging

fastmcp.utilities.mime
函数
resolve_ui_mime_type 
fastmcp.utilities
mime

fastmcp.utilities.mime
MCP Apps UI 资源的 MIME 类型常量和辅助函数。
此模块不依赖服务端或资源包，因此可以从任何位置安全导入。

函数

resolve_ui_mime_type 
resolve_ui_mime_type(uri: str, explicit_mime_type: str | None) -> str | None

返回资源 URI 对应的 MIME 类型。
对于 ui:// scheme 的资源，如果未提供显式 MIME 类型，则默认使用 UI_MIME_TYPE。
参数：
uri: 资源 URI 字符串
explicit_mime_type: 用户显式提供的 MIME 类型
返回：
解析后的 MIME 类型（显式值、UI 默认值或 None）
filesystem
openapi

