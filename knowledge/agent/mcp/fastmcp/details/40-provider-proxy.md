---
title: "MCP Proxy Provider"
source: "https://fastmcp.wiki/zh/servers/providers/proxy"
version: "latest"
---

# MCP Proxy Provider

> 原始文档来源：https://fastmcp.wiki/zh/servers/providers/proxy

---

Why Use Proxy Provider
Quick Start
Transport Bridging
Session Isolation
Shared Sessions
MCP Feature Forwarding
Disabling Features
Configuration-Based Proxies
Multi-Server Proxies
Component Prefixing
Mirrored Components
Performance Considerations
Component List Caching
Session Reuse for Stateless Backends
Advanced Usage
FastMCPProxy Class
Adding Proxied Components to Existing Server
MCP 提供方
MCP Proxy Provider

Source components from other MCP servers

版本
2.0.0
新增
The Proxy Provider sources components from another MCP server through a client connection. This lets you expose any MCP server’s tools, resources, and prompts through your own server, whether the source is local or accessed over the network.

Why Use Proxy Provider
The Proxy Provider enables:
Bridge transports: Make an HTTP server available via stdio, or vice versa
Aggregate servers: Combine multiple source servers into one unified server
Add security: Act as a controlled gateway with authentication and authorization
Simplify access: Provide a stable endpoint even if backend servers change
Source Server
FastMCP Proxy
Your Client
Source Server
FastMCP Proxy
Your Client
MCP Request (stdio)
MCP Request (HTTP/stdio/SSE)
MCP Response
MCP Response

Quick Start
版本
2.10.3
新增
Create a proxy using create_proxy():
from fastmcp.server import create_proxy

# create_proxy() accepts URLs, file paths, and transports directly
proxy = create_proxy("http://example.com/mcp", name="MyProxy")

if __name__ == "__main__":
    proxy.run()

This gives you:
Safe concurrent request handling
Automatic forwarding of MCP features (sampling, elicitation, etc.)
Session isolation to prevent context mixing
To mount a proxy inside another FastMCP server, see Mounting External Servers.

Transport Bridging
A common use case is bridging transports between servers:
from fastmcp.server import create_proxy

# Bridge HTTP server to local stdio
http_proxy = create_proxy("http://example.com/mcp/sse", name="HTTP-to-stdio")

# Run locally via stdio for Claude Desktop
if __name__ == "__main__":
    http_proxy.run()  # Defaults to stdio

Or expose a local server via HTTP:
from fastmcp.server import create_proxy

# Bridge local server to HTTP
local_proxy = create_proxy("local_server.py", name="stdio-to-HTTP")

if __name__ == "__main__":
    local_proxy.run(transport="http", host="0.0.0.0", port=8080)

Session Isolation
版本
2.10.3
新增
create_proxy() provides session isolation - each request gets its own isolated backend session:
from fastmcp.server import create_proxy

# Each request creates a fresh backend session (recommended)
proxy = create_proxy("backend_server.py")

# Multiple clients can use this proxy simultaneously:
# - Client A calls a tool → gets isolated session
# - Client B calls a tool → gets different session
# - No context mixing

Shared Sessions
If you pass an already-connected client, the proxy reuses that session:
from fastmcp import Client
from fastmcp.server import create_proxy

async with Client("backend_server.py") as connected_client:
    # This proxy reuses the connected session
    proxy = create_proxy(connected_client)

    # ⚠️ Warning: All requests share the same session

Shared sessions may cause context mixing in concurrent scenarios. Use only in single-threaded situations or with explicit synchronization.

MCP Feature Forwarding
版本
2.10.3
新增
Proxies automatically forward MCP protocol features:
Feature	Description
Roots	Filesystem root access requests
Sampling	LLM completion requests
Elicitation	User input requests
Logging	Log messages from backend
Progress	Progress notifications
from fastmcp.server import create_proxy

# All features forwarded automatically
proxy = create_proxy("advanced_backend.py")

# When the backend:
# - Requests LLM sampling → forwarded to your client
# - Logs messages → appear in your client
# - Reports progress → shown in your client

Disabling Features
Selectively disable forwarding:
from fastmcp.server.providers.proxy import ProxyClient

backend = ProxyClient(
    "backend_server.py",
    sampling_handler=None,  # Disable LLM sampling
    log_handler=None        # Disable log forwarding
)

Configuration-Based Proxies
版本
2.4.0
新增
Create proxies from configuration dictionaries:
from fastmcp.server import create_proxy

config = {
    "mcpServers": {
        "default": {
            "url": "https://example.com/mcp",
            "transport": "http"
        }
    }
}

proxy = create_proxy(config, name="Config-Based Proxy")

Multi-Server Proxies
Combine multiple servers with automatic namespacing:
from fastmcp.server import create_proxy

config = {
    "mcpServers": {
        "weather": {
            "url": "https://weather-api.example.com/mcp",
            "transport": "http"
        },
        "calendar": {
            "url": "https://calendar-api.example.com/mcp",
            "transport": "http"
        }
    }
}

# Creates unified proxy with prefixed components:
# - weather_get_forecast
# - calendar_add_event
composite = create_proxy(config, name="Composite")

Component Prefixing
Proxied components follow standard prefixing rules:
Component Type	Pattern
Tools	{prefix}_{tool_name}
Prompts	{prefix}_{prompt_name}
Resources	protocol://{prefix}/path
Templates	protocol://{prefix}/...

Mirrored Components
版本
2.10.5
新增
Components from a proxy server are “mirrored” - they reflect the remote server’s state and cannot be modified directly.
To modify a proxied component (like disabling it), create a local copy:
from fastmcp import FastMCP
from fastmcp.server import create_proxy

proxy = create_proxy("backend_server.py")

# Get mirrored tool
mirrored_tool = await proxy.get_tool("useful_tool")

# Create modifiable local copy
local_tool = mirrored_tool.copy()

# Add to your own server
my_server = FastMCP("MyServer")
my_server.add_tool(local_tool)

# Now you can control enabled state
my_server.disable(keys={local_tool.key})

Performance Considerations
Proxying introduces network latency:
Operation	Local	Proxied (HTTP)
list_tools()	1-2ms	300-400ms
call_tool()	1-2ms	200-500ms
When mounting proxy servers, this latency affects all operations on the parent server.

Component List Caching
版本
3.2.0
新增
ProxyProvider caches the backend’s component lists (tools, resources, templates, prompts) so that individual lookups — like resolving a tool by name during call_tool — don’t require a separate backend connection. The cache stores raw component metadata and is shared across all proxy sessions; per-session visibility, auth, and transforms are still applied after cache lookup by the server layer. The cache refreshes whenever an explicit list_* call is made, and entries expire after a configurable TTL (default 300 seconds).
For backends whose component lists change dynamically, disable caching by setting cache_ttl=0.
from fastmcp.server.providers.proxy import ProxyProvider, ProxyClient

# Default 300s TTL
provider = ProxyProvider(lambda: ProxyClient("http://backend/mcp"))

# Custom TTL
provider = ProxyProvider(lambda: ProxyClient("http://backend/mcp"), cache_ttl=60)

# Disable caching
provider = ProxyProvider(lambda: ProxyClient("http://backend/mcp"), cache_ttl=0)

Session Reuse for Stateless Backends
By default, each tool call opens a fresh MCP session to the backend. This is the safe default because it prevents state from leaking between requests. However, for stateless HTTP backends where there’s no session state to protect, this overhead is unnecessary.
You can reuse a single backend session by providing a client factory that returns the same client instance:
from fastmcp.server.providers.proxy import FastMCPProxy, ProxyClient

base_client = ProxyClient("http://backend:8000/mcp")
shared_client = base_client.new()

proxy = FastMCPProxy(
    client_factory=lambda: shared_client,
    name="ReusedSessionProxy",
)

This eliminates the MCP initialization handshake on every call, which can dramatically reduce latency under load. The Client uses reference counting for its session lifecycle, so concurrent callers sharing the same instance is safe.
Only reuse sessions when you know the backend is stateless (e.g. stateless HTTP). For stateful backends (stdio processes, servers that track session state), use the default fresh-session behavior to avoid context mixing.

Advanced Usage

FastMCPProxy Class
For explicit session control, use FastMCPProxy directly:
from fastmcp.server.providers.proxy import FastMCPProxy, ProxyClient

# Custom session factory
def create_client():
    return ProxyClient("backend_server.py")

proxy = FastMCPProxy(client_factory=create_client)

This gives you full control over session creation and reuse strategies.

Adding Proxied Components to Existing Server
Mount a proxy to add components from another server:
from fastmcp import FastMCP
from fastmcp.server import create_proxy

server = FastMCP("My Server")

# Add local tools
@server.tool
def local_tool() -> str:
    return "Local result"

# Mount proxied tools from another server
external = create_proxy("http://external-server/mcp")
server.mount(external)

# Now server has both local and proxied tools

文件系统提供方

Skills 提供方

x

