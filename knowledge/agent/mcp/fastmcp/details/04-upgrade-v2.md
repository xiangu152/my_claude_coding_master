---
title: "从 FastMCP 2 升级"
source: "https://fastmcp.wiki/zh/getting-started/upgrading/from-fastmcp-2"
version: "latest"
---

# 从 FastMCP 2 升级

> 原始文档来源：https://fastmcp.wiki/zh/getting-started/upgrading/from-fastmcp-2

---

v3.0.0
Install
Breaking Changes
Deprecated Features
v2.14.0
OpenAPI Parser Promotion
Removed Deprecated Features
v2.13.0
OAuth Token Key Management
升级
从 FastMCP 2 升级

在 FastMCP 版本之间升级的迁移说明

This guide covers breaking changes and migration steps when upgrading FastMCP.

v3.0.0
For most servers, upgrading to v3 is straightforward. The breaking changes below affect deprecated constructor kwargs, sync-to-async shifts, a few renamed methods, and some less commonly used features.

Install
Since you already have fastmcp installed, you need to explicitly request the new version — pip install fastmcp won’t upgrade an existing installation:
pip install --upgrade fastmcp
# or
uv add --upgrade fastmcp

If you pin versions in a requirements file or pyproject.toml, update your pin to fastmcp>=3.0.0,<4.
New repository home. As part of the v3 release, FastMCP’s GitHub repository has moved from jlowin/fastmcp to PrefectHQ/fastmcp under Prefect’s stewardship. GitHub automatically redirects existing clones and bookmarks, so nothing breaks — but you can update your local remote whenever convenient:
git remote set-url origin https://github.com/PrefectHQ/fastmcp.git

If you reference the repository URL in dependency specifications (e.g., git+https://github.com/jlowin/fastmcp.git), update those to the new location.

Copy this prompt into any LLM along with your server code to get automated upgrade guidance.

复制提示词

Breaking Changes
Transport and server settings removed from constructor
In v2, you could configure transport settings directly in the FastMCP() constructor. In v3, FastMCP() is purely about your server’s identity and behavior — transport configuration happens when you actually start serving. Passing any of the old kwargs now raises TypeError with a migration hint.
# Before
mcp = FastMCP("server", host="0.0.0.0", port=8080)
mcp.run()

# After
mcp = FastMCP("server")
mcp.run(transport="http", host="0.0.0.0", port=8080)

The full list of removed kwargs and their replacements:
host, port, log_level, debug, sse_path, streamable_http_path, json_response, stateless_http — pass to run(), run_http_async(), or http_app(), or set via environment variables (e.g. FASTMCP_HOST)
message_path — set via environment variable FASTMCP_MESSAGE_PATH only (not a run() kwarg)
on_duplicate_tools, on_duplicate_resources, on_duplicate_prompts — consolidated into a single on_duplicate= parameter
tool_serializer — return ToolResult from your tools instead
include_tags / exclude_tags — use server.enable(tags=..., only=True) / server.disable(tags=...) after construction
tool_transformations — use server.add_transform(ToolTransform(...)) after construction
OAuth storage backend changed (diskcache CVE)
The default OAuth client storage has moved from DiskStore to FileTreeStore to address a pickle deserialization vulnerability in diskcache (CVE-2025-69872).
If you were using the default storage (i.e., not passing an explicit client_storage), clients will need to re-register on their first connection after upgrading. This happens automatically — no user action required, and it’s the same flow that already occurs whenever a server restarts with in-memory storage.
If you were passing a DiskStore explicitly, you can either switch to FileTreeStore (recommended) or keep using DiskStore by adding the dependency yourself.
When switching to FileTreeStore, you must configure key and collection sanitization strategies. Without them, keys containing special characters (such as URL-based OAuth client IDs) will cause filesystem errors. See the File Storage section for the recommended setup.
Keeping DiskStore requires pip install 'py-key-value-aio[disk]', which re-introduces the vulnerable diskcache package into your dependency tree.
Component enable()/disable() moved to server
In v2, you could enable or disable individual components by calling methods on the component object itself. In v3, visibility is controlled through the server (or provider), which lets you target components by name, tag, or type without needing a reference to the object:
# Before
tool = await server.get_tool("my_tool")
tool.disable()

# After
server.disable(names={"my_tool"}, components={"tool"})

Calling .enable() or .disable() on a component object now raises NotImplementedError. See Visibility for the full API, including tag-based filtering and per-session visibility.
Listing methods renamed and return lists
The get_tools(), get_resources(), get_prompts(), and get_resource_templates() methods have been renamed to list_tools(), list_resources(), list_prompts(), and list_resource_templates(). More importantly, they now return lists instead of dicts — so code that indexes by name needs to change:
# Before
tools = await server.get_tools()
tool = tools["my_tool"]

# After
tools = await server.list_tools()
tool = next((t for t in tools if t.name == "my_tool"), None)

Prompts use Message class
Prompt functions now use FastMCP’s Message class instead of mcp.types.PromptMessage. The new class is simpler — it accepts a plain string and defaults to role="user", so most prompts become one-liners:
# Before
from mcp.types import PromptMessage, TextContent

@mcp.prompt
def my_prompt() -> PromptMessage:
    return PromptMessage(role="user", content=TextContent(type="text", text="Hello"))

# After
from fastmcp.prompts import Message

@mcp.prompt
def my_prompt() -> Message:
    return Message("Hello")

If your prompt functions return raw dicts with role and content keys, those also need to change. v2 silently coerced dicts into prompt messages, but v3 requires typed Message objects (or plain strings for single user messages):
# Before (v2 accepted this)
@mcp.prompt
def my_prompt():
    return [
        {"role": "user", "content": "Hello"},
        {"role": "assistant", "content": "How can I help?"},
    ]

# After
from fastmcp.prompts import Message

@mcp.prompt
def my_prompt() -> list[Message]:
    return [
        Message("Hello"),
        Message("How can I help?", role="assistant"),
    ]

Context state methods are async
ctx.set_state() and ctx.get_state() are now async because state in v3 is session-scoped and backed by a pluggable storage backend (rather than a simple dict). This means state persists across multiple tool calls within the same session:
# Before
ctx.set_state("key", "value")
value = ctx.get_state("key")

# After
await ctx.set_state("key", "value")
value = await ctx.get_state("key")

State values must also be JSON-serializable by default (dicts, lists, strings, numbers, etc.). If you need to store non-serializable values like an HTTP client, pass serializable=False — these values are request-scoped and only available during the current tool call:
await ctx.set_state("client", my_http_client, serializable=False)

Mounted servers have isolated state stores
Each FastMCP instance has its own state store. In v2 this wasn’t noticeable because mounted tools ran in the parent’s context, but in v3’s provider architecture each server is isolated. Non-serializable state (serializable=False) is request-scoped and automatically shared across mount boundaries. For serializable state, pass the same session_state_store to both servers:
from fastmcp import FastMCP
from key_value.aio.stores.memory import MemoryStore

store = MemoryStore()
parent = FastMCP("Parent", session_state_store=store)
child = FastMCP("Child", session_state_store=store)
parent.mount(child, namespace="child")

Auth provider environment variables removed
In v2, auth providers like GitHubProvider could auto-load configuration from environment variables with a FASTMCP_SERVER_AUTH_* prefix. This magic has been removed — pass values explicitly:
# Before (v2) — client_id and client_secret loaded automatically
# from FASTMCP_SERVER_AUTH_GITHUB_CLIENT_ID, etc.
auth = GitHubProvider()

# After (v3) — pass values explicitly
import os
from fastmcp.server.auth.providers.github import GitHubProvider

auth = GitHubProvider(
    client_id=os.environ["GITHUB_CLIENT_ID"],
    client_secret=os.environ["GITHUB_CLIENT_SECRET"],
)

WSTransport removed
The deprecated WebSocket client transport has been removed. Use StreamableHttpTransport instead:
# Before
from fastmcp.client.transports import WSTransport
transport = WSTransport("ws://localhost:8000/ws")

# After
from fastmcp.client.transports import StreamableHttpTransport
transport = StreamableHttpTransport("http://localhost:8000/mcp")

OpenAPI timeout parameter removed
OpenAPIProvider no longer accepts a timeout parameter. Configure timeout on the httpx client directly. The client parameter is also now optional — when omitted, a default client is created from the spec’s servers URL with a 30-second timeout:
# Before
provider = OpenAPIProvider(spec, client, timeout=60)

# After
client = httpx.AsyncClient(base_url="https://api.example.com", timeout=60)
provider = OpenAPIProvider(spec, client)

Metadata namespace renamed
The FastMCP metadata key in component meta dicts changed from _fastmcp to fastmcp. If you read metadata from tool or resource objects, update the key:
# Before
tags = tool.meta.get("_fastmcp", {}).get("tags", [])

# After
tags = tool.meta.get("fastmcp", {}).get("tags", [])

Metadata is now always included — the include_fastmcp_meta parameter has been removed from FastMCP() and to_mcp_tool(), so there is no way to suppress it.
Server banner environment variable renamed
FASTMCP_SHOW_CLI_BANNER is now FASTMCP_SHOW_SERVER_BANNER.
Decorators return functions
In v2, @mcp.tool transformed your function into a FunctionTool object. In v3, decorators return your original function unchanged — which means decorated functions stay callable for testing, reuse, and composition:
@mcp.tool
def greet(name: str) -> str:
    return f"Hello, {name}!"

greet("World")  # Works! Returns "Hello, World!"

If you have code that treats the decorated result as a FunctionTool (e.g., accessing .name or .description), set FASTMCP_DECORATOR_MODE=object for v2 compatibility. This escape hatch is itself deprecated and will be removed in a future release.
Background tasks require optional dependency
FastMCP’s background task system (SEP-1686) is now behind an optional extra. If your server uses background tasks, install with:
pip install "fastmcp[tasks]"

Without the extra, configuring a tool with task=True or TaskConfig will raise an import error at runtime. See Background Tasks for details.

Deprecated Features
These still work but emit warnings. Update when convenient.
mount() prefix → namespace
# Deprecated
main.mount(subserver, prefix="api")

# New
main.mount(subserver, namespace="api")

import_server() → mount()
# Deprecated
main.import_server(subserver)

# New
main.mount(subserver)

Module import paths for proxy and OpenAPI
The proxy and OpenAPI modules have moved under providers to reflect v3’s provider-based architecture:
# Deprecated
from fastmcp.server.proxy import FastMCPProxy
from fastmcp.server.openapi import FastMCPOpenAPI

# New
from fastmcp.server.providers.proxy import FastMCPProxy
from fastmcp.server.providers.openapi import OpenAPIProvider

FastMCPOpenAPI itself is deprecated — use FastMCP with an OpenAPIProvider instead:
# Deprecated
from fastmcp.server.openapi import FastMCPOpenAPI
server = FastMCPOpenAPI(spec, client)

# New
from fastmcp import FastMCP
from fastmcp.server.providers.openapi import OpenAPIProvider
server = FastMCP("my_api", providers=[OpenAPIProvider(spec, client)])

add_tool_transformation() → add_transform()
# Deprecated
mcp.add_tool_transformation("name", config)

# New
from fastmcp.server.transforms import ToolTransform
mcp.add_transform(ToolTransform({"name": config}))

FastMCP.as_proxy() → create_proxy()
# Deprecated
proxy = FastMCP.as_proxy("http://example.com/mcp")

# New
from fastmcp.server import create_proxy
proxy = create_proxy("http://example.com/mcp")

v2.14.0

OpenAPI Parser Promotion
The experimental OpenAPI parser is now standard. Update imports:
# Before
from fastmcp.experimental.server.openapi import FastMCPOpenAPI

# After
from fastmcp.server.openapi import FastMCPOpenAPI

Removed Deprecated Features
BearerAuthProvider → use JWTVerifier
Context.get_http_request() → use get_http_request() from dependencies
from fastmcp import Image → use from fastmcp.utilities.types import Image
FastMCP(dependencies=[...]) → use fastmcp.json configuration
FastMCPProxy(client=...) → use client_factory=lambda: ...
output_schema=False → use output_schema=None

v2.13.0

OAuth Token Key Management
The OAuth proxy now issues its own JWT tokens. For production, provide explicit keys:
auth = GitHubProvider(
    client_id=os.environ["GITHUB_CLIENT_ID"],
    client_secret=os.environ["GITHUB_CLIENT_SECRET"],
    base_url="https://your-server.com",
    jwt_signing_key=os.environ["JWT_SIGNING_KEY"],
    client_storage=RedisStore(host="redis.example.com"),
)

See OAuth Token Security for details.
认证工具

从 MCP SDK 升级

x

