---
title: "Bearer Token 认证"
source: "https://fastmcp.wiki/zh/clients/auth/bearer"
version: "latest"
---

# Bearer Token 认证

> 原始文档来源：https://fastmcp.wiki/zh/clients/auth/bearer

---

客户端用法
BearerAuth 辅助类
自定义请求头
身份认证
Bearer Token 认证

使用 Bearer token 认证你的 FastMCP 客户端。

版本
2.6.0
新增
Bearer Token 认证只适用于基于 HTTP 的传输。
你可以提供一个有效的访问令牌，让 FastMCP 客户端使用 Bearer 认证。这最适合服务账号、长期有效的 API key、CI/CD、认证由其他系统单独管理的应用，或其他非交互式认证方式。
Bearer token 是用于认证请求的 JSON Web Token (JWT)。它最常见的用法是在 HTTP 请求的 Authorization 头中使用 Bearer 方案：
Authorization: Bearer <token>

客户端用法
使用已有 Bearer token 最直接的方式，是把它作为字符串传给 fastmcp.Client 或传输实例的 auth 参数。FastMCP 会自动将其格式化为适用于 Authorization 头和 bearer 方案的形式。
如果你使用字符串形式的令牌，不要包含 Bearer 前缀。FastMCP 会为你添加。
from fastmcp import Client

async with Client(
    "https://your-server.fastmcp.app/mcp", 
    auth="<your-token>",
) as client:
    await client.ping()

你也可以向传输实例提供 Bearer token，例如 StreamableHttpTransport 或 SSETransport：
from fastmcp import Client
from fastmcp.client.transports import StreamableHttpTransport

transport = StreamableHttpTransport(
    "http://your-server.fastmcp.app/mcp", 
    auth="<your-token>",
)

async with Client(transport) as client:
    await client.ping()

BearerAuth 辅助类
如果你希望写法更显式，而不是依赖 FastMCP 转换字符串令牌，可以直接使用实现了 httpx.Auth 接口的 BearerAuth 类。
from fastmcp import Client
from fastmcp.client.auth import BearerAuth

async with Client(
    "https://your-server.fastmcp.app/mcp", 
    auth=BearerAuth(token="<your-token>"),
) as client:
    await client.ping()

自定义请求头
如果 MCP 服务端期望自定义请求头或令牌方案，你可以在传输上手动设置客户端的 headers，而不是使用 auth 参数：
from fastmcp import Client
from fastmcp.client.transports import StreamableHttpTransport

async with Client(
    transport=StreamableHttpTransport(
        "https://your-server.fastmcp.app/mcp", 
        headers={"X-API-Key": "<your-token>"},
    ),
) as client:
    await client.ping()

CIMD 认证

Auth0 OAuth 🤝 FastMCP

x

