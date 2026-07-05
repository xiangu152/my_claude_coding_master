---
title: "欢迎使用 FastMCP"
source: "https://fastmcp.wiki/zh/getting-started/welcome"
version: "latest"
---

# 欢迎使用 FastMCP

> 原始文档来源：https://fastmcp.wiki/zh/getting-started/welcome

---

开始
欢迎使用 FastMCP

以快速、Pythonic 的方式构建 MCP 服务端、客户端和应用。

FastMCP 是构建 MCP 应用的标准框架。 Model Context Protocol（MCP）将 LLM 与工具和数据连接起来。FastMCP 提供了从原型到生产所需的一切：构建暴露能力的服务端，将客户端连接到任何 MCP 服务，并为你的工具提供交互式 UI：
from fastmcp import FastMCP

mcp = FastMCP("Demo 🚀")

@mcp.tool
def add(a: int, b: int) -> int:
    """将两个数字相加"""
    return a + b

if __name__ == "__main__":
    mcp.run()

快速行动，构建产品
Model Context Protocol（MCP）让你可以把工具和数据开放给 agents 使用。但要构建一个真正好用的 MCP 应用，比看起来更难。
FastMCP 会处理这些复杂性。用一个 Python 函数声明工具，schema、校验和文档就会自动生成。用 URL 连接服务端，传输协商、身份认证和协议生命周期都会自动管理。你只需专注业务逻辑，MCP 部分自然运转：在 FastMCP 中，最佳实践是内置的。
这就是 FastMCP 成为 MCP 标准开发框架的原因。 FastMCP 1.0 于 2024 年被纳入官方 MCP Python SDK。如今，这个持续维护的独立项目每天下载量达到百万级，并且某个版本的 FastMCP 支撑了所有语言中约 70% 的 MCP 服务端。
FastMCP 有三大支柱：
服务端
向 LLM 暴露工具、资源和提示词。
应用
为工具提供可直接在对话中渲染的交互式 UI。
客户端
连接任何 MCP 服务端：本地或远程、编程式或 CLI。
服务端 将你的 Python 函数包装成符合 MCP 的工具、资源和提示词。客户端 以完整协议支持连接任意服务端。应用 则为工具提供可直接在对话中渲染的交互式 UI。
准备开始构建了吗？可以从安装指南开始，或直接进入快速开始。
FastMCP 由 Prefect 用心打造。

使用 Horizon 在生产环境运行 FastMCP
FastMCP 是构建 MCP 服务端的标准方式。Prefect Horizon 是用于安全运行 MCP 服务端的企业级 MCP gateway。
Horizon 由 FastMCP 团队构建，汇集了我们在交付全球最受欢迎 MCP 框架过程中积累的最佳实践。
从 GitHub 部署 FastMCP 服务端，支持分支预览和即时回滚。为公司使用的每个 MCP 创建私有 registry。通过 SSO 和工具级 RBAC 保护访问。获得覆盖整个 MCP stack 的审计日志、可观测性和治理能力。将已批准的工具重新组合成面向团队和 agents 的专用 endpoints。
从 FastMCP 开始。使用 Horizon 扩展 →
本文档反映 FastMCP 的 main 分支，也就是说它始终对应最新开发版本。功能通常会用版本徽章标记（例如 New in version: 3.0.0），说明它们是在哪个版本引入的。请注意，这可能包含尚未发布的功能。

面向 LLM 友好的文档
FastMCP 文档提供多种面向 LLM 友好的格式：

MCP 服务端
FastMCP 文档可以通过 MCP 访问！服务端 URL 是 https://gofastmcp.com/mcp。
事实上，你可以使用 FastMCP 搜索 FastMCP 文档：
import asyncio
from fastmcp import Client

async def main():
    async with Client("https://gofastmcp.com/mcp") as client:
        result = await client.call_tool(
            name="search_fast_mcp",
            arguments={"query": "deploy a FastMCP server"}
        )
    print(result)

asyncio.run(main())

文本格式
文档也提供 llms.txt 格式：
llms.txt - 列出所有文档页面的 sitemap
llms-full.txt - 单文件形式的完整文档（可能超过上下文窗口）
任何页面都可以通过在 URL 后追加 .md 以 Markdown 形式访问。例如，本页面对应 https://gofastmcp.com/getting-started/welcome.md。
你也可以在键盘上按下 “Cmd+C”（Windows 上为 “Ctrl+C”），将任意页面复制为 Markdown。
安装

x

