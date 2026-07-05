---
title: "从 MCP Low-Level SDK 升级"
source: "https://fastmcp.wiki/zh/getting-started/upgrading/from-low-level-sdk"
version: "latest"
---

# 从 MCP Low-Level SDK 升级

> 原始文档来源：https://fastmcp.wiki/zh/getting-started/upgrading/from-low-level-sdk

---

Install
Server and Transport
Tools
Type Mapping
Return Values
Resources
Prompts
Request Context
Complete Example
What’s Next
升级
从 MCP Low-Level SDK 升级

将你的 MCP 服务端从低层 Python SDK 的 Server 类升级到 FastMCP

If you’ve been building MCP servers directly on the mcp package’s Server class — writing list_tools() and call_tool() handlers, hand-crafting JSON Schema dicts, and wiring up transport boilerplate — this guide is for you. FastMCP replaces all of that machinery with a declarative, Pythonic API where your functions are the protocol surface.
The core idea: instead of telling the SDK what your tools look like and then separately implementing them, you write ordinary Python functions and let FastMCP derive the protocol layer from your code. Type hints become JSON Schema. Docstrings become descriptions. Return values are serialized automatically. The plumbing you wrote to satisfy the protocol just disappears.
This guide covers upgrading from v1 of the mcp package. We’ll provide a separate guide when v2 ships.
Already using FastMCP 1.0 via from mcp.server.fastmcp import FastMCP? Your upgrade is simpler — see the FastMCP 1.0 upgrade guide instead.

Copy this prompt into any LLM along with your server code to get automated upgrade guidance.

复制提示词

Install
pip install --upgrade fastmcp
# or
uv add fastmcp

FastMCP includes the mcp package as a transitive dependency, so you don’t lose access to anything.

Server and Transport
The Server class requires you to choose a transport, connect streams, build initialization options, and run an event loop. FastMCP collapses all of that into a constructor and a run() call.
Before
After
import asyncio
from mcp.server import Server
from mcp.server.stdio import stdio_server

server = Server("my-server")

# ... register handlers ...

async def main():
    async with stdio_server() as (read_stream, write_stream):
        await server.run(
            read_stream,
            write_stream,
            server.create_initialization_options(),
        )

asyncio.run(main())

Need HTTP instead of stdio? With the Server class, you’d wire up Starlette routes and SseServerTransport or StreamableHTTPSessionManager. With FastMCP:
mcp.run(transport="http", host="0.0.0.0", port=8000)

Tools
This is where the difference is most dramatic. The Server class requires two handlers — one to describe your tools (with hand-written JSON Schema) and another to dispatch calls by name. FastMCP eliminates both by deriving everything from your function signature.
Before
After
import mcp.types as types
from mcp.server import Server

server = Server("math")

@server.list_tools()
async def list_tools() -> list[types.Tool]:
    return [
        types.Tool(
            name="add",
            description="Add two numbers",
            inputSchema={
                "type": "object",
                "properties": {
                    "a": {"type": "number"},
                    "b": {"type": "number"},
                },
                "required": ["a", "b"],
            },
        ),
        types.Tool(
            name="multiply",
            description="Multiply two numbers",
            inputSchema={
                "type": "object",
                "properties": {
                    "a": {"type": "number"},
                    "b": {"type": "number"},
                },
                "required": ["a", "b"],
            },
        ),
    ]

@server.call_tool()
async def call_tool(
    name: str, arguments: dict
) -> list[types.TextContent]:
    if name == "add":
        result = arguments["a"] + arguments["b"]
        return [types.TextContent(type="text", text=str(result))]
    elif name == "multiply":
        result = arguments["a"] * arguments["b"]
        return [types.TextContent(type="text", text=str(result))]
    raise ValueError(f"Unknown tool: {name}")

Each @mcp.tool function is self-contained: its name becomes the tool name, its docstring becomes the description, its type annotations become the JSON Schema, and its return value is serialized automatically. No routing. No schema dictionaries. No content-type wrappers.

Type Mapping
When converting your inputSchema to Python type hints:
JSON Schema	Python Type
{"type": "string"}	str
{"type": "number"}	float
{"type": "integer"}	int
{"type": "boolean"}	bool
{"type": "array", "items": {"type": "string"}}	list[str]
{"type": "object"}	dict
Optional property (not in required)	param: str | None = None

Return Values
With the Server class, tools return list[types.TextContent | types.ImageContent | ...]. In FastMCP, return plain Python values — strings, numbers, dicts, lists, dataclasses, Pydantic models — and serialization is handled for you.
For images or other non-text content, FastMCP provides helpers:
from fastmcp import FastMCP
from fastmcp.utilities.types import Image

mcp = FastMCP("media")

@mcp.tool
def create_chart(data: list[float]) -> Image:
    """Generate a chart from data."""
    png_bytes = generate_chart(data)  # your logic
    return Image(data=png_bytes, format="png")

Resources
The Server class uses three handlers for resources: list_resources() to enumerate them, list_resource_templates() for URI templates, and read_resource() to serve content — all with manual routing by URI. FastMCP replaces all three with per-resource decorators.
Before
After
import json
import mcp.types as types
from mcp.server import Server
from pydantic import AnyUrl

server = Server("data")

@server.list_resources()
async def list_resources() -> list[types.Resource]:
    return [
        types.Resource(
            uri=AnyUrl("config://app"),
            name="app_config",
            description="Application configuration",
            mimeType="application/json",
        ),
        types.Resource(
            uri=AnyUrl("config://features"),
            name="feature_flags",
            description="Active feature flags",
            mimeType="application/json",
        ),
    ]

@server.list_resource_templates()
async def list_resource_templates() -> list[types.ResourceTemplate]:
    return [
        types.ResourceTemplate(
            uriTemplate="users://{user_id}/profile",
            name="user_profile",
            description="User profile by ID",
        ),
        types.ResourceTemplate(
            uriTemplate="projects://{project_id}/status",
            name="project_status",
            description="Project status by ID",
        ),
    ]

@server.read_resource()
async def read_resource(uri: AnyUrl) -> str:
    uri_str = str(uri)
    if uri_str == "config://app":
        return json.dumps({"debug": False, "version": "1.0"})
    if uri_str == "config://features":
        return json.dumps({"dark_mode": True, "beta": False})
    if uri_str.startswith("users://"):
        user_id = uri_str.split("/")[2]
        return json.dumps({"id": user_id, "name": f"User {user_id}"})
    if uri_str.startswith("projects://"):
        project_id = uri_str.split("/")[2]
        return json.dumps({"id": project_id, "status": "active"})
    raise ValueError(f"Unknown resource: {uri}")

Static resources and URI templates use the same @mcp.resource decorator — FastMCP detects {placeholders} in the URI and automatically registers a template. The function parameter user_id maps directly to the {user_id} placeholder.

Prompts
Same pattern: the Server class uses list_prompts() and get_prompt() with manual routing. FastMCP uses one decorator per prompt.
Before
After
import mcp.types as types
from mcp.server import Server

server = Server("prompts")

@server.list_prompts()
async def list_prompts() -> list[types.Prompt]:
    return [
        types.Prompt(
            name="review_code",
            description="Review code for issues",
            arguments=[
                types.PromptArgument(
                    name="code",
                    description="The code to review",
                    required=True,
                ),
                types.PromptArgument(
                    name="language",
                    description="Programming language",
                    required=False,
                ),
            ],
        )
    ]

@server.get_prompt()
async def get_prompt(
    name: str, arguments: dict[str, str] | None
) -> types.GetPromptResult:
    if name == "review_code":
        code = (arguments or {}).get("code", "")
        language = (arguments or {}).get("language", "")
        lang_note = f" (written in {language})" if language else ""
        return types.GetPromptResult(
            description="Code review prompt",
            messages=[
                types.PromptMessage(
                    role="user",
                    content=types.TextContent(
                        type="text",
                        text=f"Please review this code{lang_note}:\n\n{code}",
                    ),
                )
            ],
        )
    raise ValueError(f"Unknown prompt: {name}")

Returning a str from a prompt function automatically wraps it as a user message. For multi-turn prompts, return a list[Message]:
from fastmcp import FastMCP
from fastmcp.prompts import Message

mcp = FastMCP("prompts")

@mcp.prompt
def debug_session(error: str) -> list[Message]:
    """Start a debugging conversation"""
    return [
        Message(f"I'm seeing this error:\n\n{error}"),
        Message("I'll help you debug that. Can you share the relevant code?", role="assistant"),
    ]

Request Context
The Server class exposes request context through server.request_context, which gives you the raw ServerSession for sending notifications. FastMCP replaces this with a typed Context object injected into any function that declares it.
Before
After
import mcp.types as types
from mcp.server import Server

server = Server("worker")

@server.call_tool()
async def call_tool(name: str, arguments: dict):
    if name == "process_data":
        ctx = server.request_context
        await ctx.session.send_log_message(
            level="info", data="Starting processing..."
        )
        # ... do work ...
        await ctx.session.send_log_message(
            level="info", data="Done!"
        )
        return [types.TextContent(type="text", text="Processed")]

The Context object provides logging (ctx.debug(), ctx.info(), ctx.warning(), ctx.error()), progress reporting (ctx.report_progress()), resource subscriptions, session state, and more. See Context for the full API.

Complete Example
A full server upgrade, showing how all the pieces fit together:
Before
After
import asyncio
import json
import mcp.types as types
from mcp.server import Server
from mcp.server.stdio import stdio_server
from pydantic import AnyUrl

server = Server("demo")

@server.list_tools()
async def list_tools() -> list[types.Tool]:
    return [
        types.Tool(
            name="greet",
            description="Greet someone by name",
            inputSchema={
                "type": "object",
                "properties": {
                    "name": {"type": "string"},
                },
                "required": ["name"],
            },
        )
    ]

@server.call_tool()
async def call_tool(name: str, arguments: dict) -> list[types.TextContent]:
    if name == "greet":
        return [types.TextContent(type="text", text=f"Hello, {arguments['name']}!")]
    raise ValueError(f"Unknown tool: {name}")

@server.list_resources()
async def list_resources() -> list[types.Resource]:
    return [
        types.Resource(
            uri=AnyUrl("info://version"),
            name="version",
            description="Server version",
        )
    ]

@server.read_resource()
async def read_resource(uri: AnyUrl) -> str:
    if str(uri) == "info://version":
        return json.dumps({"version": "1.0.0"})
    raise ValueError(f"Unknown resource: {uri}")

@server.list_prompts()
async def list_prompts() -> list[types.Prompt]:
    return [
        types.Prompt(
            name="summarize",
            description="Summarize text",
            arguments=[
                types.PromptArgument(name="text", required=True)
            ],
        )
    ]

@server.get_prompt()
async def get_prompt(
    name: str, arguments: dict[str, str] | None
) -> types.GetPromptResult:
    if name == "summarize":
        return types.GetPromptResult(
            description="Summarize text",
            messages=[
                types.PromptMessage(
                    role="user",
                    content=types.TextContent(
                        type="text",
                        text=f"Summarize:\n\n{(arguments or {}).get('text', '')}",
                    ),
                )
            ],
        )
    raise ValueError(f"Unknown prompt: {name}")

async def main():
    async with stdio_server() as (read_stream, write_stream):
        await server.run(
            read_stream, write_stream,
            server.create_initialization_options(),
        )

asyncio.run(main())

See all 86 lines

What’s Next
Once you’ve upgraded, you have access to everything FastMCP provides beyond the basics:
Server composition — Mount sub-servers to build modular applications
Middleware — Add logging, rate limiting, error handling, and caching
Proxy servers — Create a proxy to any existing MCP server
OpenAPI integration — Generate an MCP server from an OpenAPI spec
Authentication — Built-in OAuth and token verification
Testing — Test your server directly in Python without running a subprocess
Explore the full documentation at gofastmcp.com.
从 MCP SDK 升级

贡献指南

x

