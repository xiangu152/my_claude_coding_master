---
title: "测试服务端"
source: "https://fastmcp.wiki/zh/servers/testing"
version: "latest"
---

# 测试服务端

> 原始文档来源：https://fastmcp.wiki/zh/servers/testing

---

前置条件
使用 Pytest Fixtures 测试
部署
测试你的 FastMCP 服务端

如何测试你的 FastMCP 服务端。

确保 FastMCP 服务端可靠且易于维护的最佳方式就是测试它！FastMCP Client 与 Pytest 结合，为测试 FastMCP 服务端提供了一种简单而强大的方式。

前置条件
测试 FastMCP 服务端需要 pytest-asyncio 来处理异步测试函数和 fixtures。请将其安装为开发依赖：
pip install pytest-asyncio

我们建议在 pyproject.toml 中将 asyncio mode 设置为 auto，让 pytest 自动处理异步测试：
[tool.pytest.ini_options]
asyncio_mode = "auto"

这样就无需为每个异步测试添加 @pytest.mark.asyncio 装饰器。

使用 Pytest Fixtures 测试
使用 Pytest Fixtures，你可以将 FastMCP 服务端包装在 Client 实例中，从而快速、轻松地与服务端交互。当你构建自己的 MCP 服务端时，这尤其有用；它让你在开发期间无需使用 MCP Inspector 之类的独立工具，从而获得紧密的开发循环：
import pytest
from fastmcp.client import Client
from fastmcp.client.transports import FastMCPTransport

from my_project.main import mcp

@pytest.fixture
async def main_mcp_client():
    async with Client(transport=mcp) as mcp_client:
        yield mcp_client

async def test_list_tools(main_mcp_client: Client[FastMCPTransport]):
    list_tools = await main_mcp_client.list_tools()

    assert len(list_tools) == 5

对于来自 MCP 服务端的复杂数据结构，我们推荐使用 inline-snapshot library 进行断言。这个库可以帮助你编写易读、易懂的测试，并且在数据结构变化时也容易更新。
from inline_snapshot import snapshot

async def test_list_tools(main_mcp_client: Client[FastMCPTransport]):
    list_tools = await main_mcp_client.list_tools()

    assert list_tools == snapshot()

只需运行 pytest --inline-snapshot=fix,create，即可用实际数据填充 snapshot()。
对于会变化的值，你可以使用 dirty-equals 库，对动态或非确定性值执行灵活的相等性断言。
使用 pytest 的 parametrize 装饰器，你可以轻松用各种输入测试工具。
import pytest
from my_project.main import mcp

from fastmcp.client import Client
from fastmcp.client.transports import FastMCPTransport
@pytest.fixture
async def main_mcp_client():
    async with Client(mcp) as client:
        yield client

@pytest.mark.parametrize(
    "first_number, second_number, expected",
    [
        (1, 2, 3),
        (2, 3, 5),
        (3, 4, 7),
    ],
)
async def test_add(
    first_number: int,
    second_number: int,
    expected: int,
    main_mcp_client: Client[FastMCPTransport],
):
    result = await main_mcp_client.call_tool(
        name="add", arguments={"x": first_number, "y": second_number}
    )
    assert result.data is not None
    assert isinstance(result.data, int)
    assert result.data == expected

FastMCP 仓库包含数千个测试，覆盖 FastMCP Client 和 Server。从连接远程 MCP 服务端，到测试工具、资源和提示词，都有相关示例，欢迎参考获取灵感！
项目配置

OpenTelemetry

x

