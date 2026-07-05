---
title: "调用工具"
source: "https://fastmcp.wiki/zh/clients/tools"
version: "latest"
---

# 调用工具

> 原始文档来源：https://fastmcp.wiki/zh/clients/tools

---

基本执行
执行选项
结构化结果
错误处理
发送元数据
原始协议访问
操作
调用工具

执行服务端工具并处理结构化结果。

版本
2.0.0
新增
当你需要执行服务端函数并处理其结果时，请使用此功能。
工具是 MCP 服务端暴露的可执行函数。客户端的 call_tool() 方法会按名称和参数执行工具，并返回结构化结果。

基本执行
async with client:
    result = await client.call_tool("add", {"a": 5, "b": 3})
    # result -> 包含结构化和非结构化数据的 CallToolResult

    # 访问结构化数据（自动反序列化）
    print(result.data)  # 8

    # 访问传统内容块
    print(result.content[0].text)  # "8"

参数以字典形式传入。对于多服务端客户端，工具名称会自动加上服务端名称前缀（例如 weather 服务端上名为 get_forecast 的工具会变成 weather_get_forecast）。

执行选项
call_tool() 方法支持超时控制和进度监控：
async with client:
    # 设置超时（执行超过 2 秒则中止）
    result = await client.call_tool(
        "long_running_task",
        {"param": "value"},
        timeout=2.0
    )

    # 设置进度处理器
    result = await client.call_tool(
        "long_running_task",
        {"param": "value"},
        progress_handler=my_progress_handler
    )

结构化结果
版本
2.10.0
新增
工具执行会返回一个 CallToolResult 对象。.data 属性提供从服务端输出 schema 重建出的完整 Python 对象，包括 datetime 和 UUID 等复杂类型。
from datetime import datetime
from uuid import UUID

async with client:
    result = await client.call_tool("get_weather", {"city": "London"})

    # FastMCP 重建完整 Python 对象
    weather = result.data
    print(f"Temperature: {weather.temperature}C at {weather.timestamp}")

    # 复杂类型会被正确反序列化
    assert isinstance(weather.timestamp, datetime)
    assert isinstance(weather.station_id, UUID)

    # 也可以访问原始结构化 JSON
    print(f"Raw JSON: {result.structured_content}")

CallToolResult 属性

.data
Any
完整 Python 对象，支持复杂类型（datetime、UUID、自定义类）。FastMCP 独有。

.content
list[mcp.types.ContentBlock]
标准 MCP 内容块（TextContent、ImageContent、AudioContent 等）。

.structured_content
dict[str, Any] | None
服务端发送的标准 MCP 结构化 JSON 数据。

.is_error
bool
表示工具执行是否失败的布尔值。
对于没有输出 schema 的工具，或反序列化失败时，.data 会是 None。此时请回退到内容块：
async with client:
    result = await client.call_tool("legacy_tool", {"param": "value"})

    if result.data is not None:
        print(f"Structured: {result.data}")
    else:
        for content in result.content:
            if hasattr(content, 'text'):
                print(f"Text result: {content.text}")

FastMCP 服务端会自动将原始类型结果（如 int、str、bool）包装在 {"result": value} 结构中。FastMCP 客户端会自动解包，因此你可以在 .data 中得到原始值。

错误处理
默认情况下，如果工具执行失败，call_tool() 会抛出 ToolError：
from fastmcp.exceptions import ToolError

async with client:
    try:
        result = await client.call_tool("potentially_failing_tool", {"param": "value"})
        print("Tool succeeded:", result.data)
    except ToolError as e:
        print(f"Tool failed: {e}")

如果想手动处理错误而不是捕获异常，请禁用自动抛错：
async with client:
    result = await client.call_tool(
        "potentially_failing_tool",
        {"param": "value"},
        raise_on_error=False
    )

    if result.is_error:
        print(f"Tool failed: {result.content[0].text}")
    else:
        print(f"Tool succeeded: {result.data}")

发送元数据
版本
2.13.1
新增
meta 参数会随工具调用发送辅助信息，用于可观测性、调试或客户端标识：
async with client:
    result = await client.call_tool(
        name="send_email",
        arguments={
            "to": "user@example.com",
            "subject": "Hello",
            "body": "Welcome!"
        },
        meta={
            "trace_id": "abc-123",
            "request_source": "mobile_app"
        }
    )

要了解服务端如何访问这些数据，请参见客户端元数据。

原始协议访问
如需完全控制，请使用 call_tool_mcp()，它会返回原始 MCP 协议对象：
async with client:
    result = await client.call_tool_mcp("my_tool", {"param": "value"})
    # result -> mcp.types.CallToolResult

    if result.isError:
        print(f"Tool failed: {result.content}")
    else:
        print(f"Tool succeeded: {result.content}")
        # 注意：call_tool_mcp() 不会自动反序列化

fastmcp-remote

读取资源

x

