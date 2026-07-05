---
title: "SDK: Settings, Types, Exceptions"
source: "https://fastmcp.wiki/zh/python-sdk/fastmcp-settings"
version: "latest"
---

# SDK: Settings, Types, Exceptions

> 原始文档来源：https://fastmcp.wiki/zh/python-sdk/fastmcp-settings (Python SDK API Reference)

---

fastmcp.settings
类
DocketSettings 
Settings 
get_setting 
set_setting 
normalize_log_level 
settings

fastmcp.settings

类

DocketSettings 
Docket worker 配置。

Settings 
FastMCP 设置。
方法：

get_setting 
get_setting(self, attr: str) -> Any

获取设置。如果设置包含一个或多个 __，则会被视为嵌套设置。

set_setting 
set_setting(self, attr: str, value: Any) -> None

设置某项配置。如果设置包含一个或多个 __，则会被视为嵌套设置。

normalize_log_level 
normalize_log_level(cls, v)

server
telemetry

fastmcp.types
types

fastmcp.types
FastMCP 工具参数的可复用类型注解。
这些类型可用于工具函数签名，以影响参数在 UI（例如 fastmcp dev apps）中的呈现方式，以及在 JSON Schema 中的序列化方式。
示例::
from fastmcp import FastMCP from fastmcp.types import Textarea
mcp = FastMCP(“demo”)
@mcp.tool() def run_query(sql: Textarea) -> str: …
tools
__init__

fastmcp.exceptions
类
FastMCPDeprecationWarning 
FastMCPError 
ValidationError 
ResourceError 
ToolError 
PromptError 
InvalidSignature 
ClientError 
NotFoundError 
DisabledError 
AuthorizationError 
exceptions

fastmcp.exceptions
FastMCP 的自定义异常。

类

FastMCPDeprecationWarning 
FastMCP API 的弃用警告。
DeprecationWarning 的子类，因此标准警告过滤器仍然适用，同时 FastMCP 可以选择性启用自己的警告，而不影响进程中的其他库。

FastMCPError 
FastMCP 的基础错误。

ValidationError 
验证参数或返回值时发生的错误。

ResourceError 
资源操作中的错误。

ToolError 
工具操作中的错误。

PromptError 
提示词操作中的错误。

InvalidSignature 
用于 FastMCP 时的无效签名。

ClientError 
客户端操作中的错误。

NotFoundError 
对象未找到。

DisabledError 
对象已禁用。

AuthorizationError 
授权检查失败时的错误。
dependencies
mcp_config

