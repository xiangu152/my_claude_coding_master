---
title: "客户端根目录"
source: "https://fastmcp.wiki/zh/clients/roots"
version: "latest"
---

# 客户端根目录

> 原始文档来源：https://fastmcp.wiki/zh/clients/roots

---

静态根目录
动态根目录
操作
客户端根目录

向 MCP 服务端提供本地上下文和资源边界。

版本
2.0.0
新增
当你需要告诉服务端客户端可以访问哪些本地资源时，请使用此功能。
根目录会告知服务端客户端可提供哪些资源。服务端可以使用这些信息调整行为，或提供更相关的响应。

静态根目录
创建客户端时提供根目录列表：
from fastmcp import Client

client = Client(
    "my_mcp_server.py",
    roots=["/path/to/root1", "/path/to/root2"]
)

动态根目录
当服务端请求根目录时，使用回调动态计算：
from fastmcp import Client
from fastmcp.client.roots import RequestContext

async def roots_callback(context: RequestContext) -> list[str]:
    print(f"服务端请求了根目录（请求 ID：{context.request_id}）")
    return ["/path/to/root1", "/path/to/root2"]

client = Client(
    "my_mcp_server.py",
    roots=roots_callback
)

服务端日志

通知

x

