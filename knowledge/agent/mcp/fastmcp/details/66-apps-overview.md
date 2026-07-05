---
title: "应用概览"
source: "https://fastmcp.wiki/zh/apps/overview"
version: "latest"
---

# 应用概览

> 原始文档来源：https://fastmcp.wiki/zh/apps/overview

---

应用
应用

为工具提供可直接在对话中渲染的交互式 UI。

版本
3.0.0
新增
FastMCP app 是一种返回交互式 UI 而不是文本的工具。当宿主调用它时，用户会在对话中直接看到图表、表格、表单，甚至完整 dashboard，并且排序、搜索、tooltip 和状态都能正常工作。
上面的 dashboard 是一个 Prefab 展示示例，可以让你感受 FastMCP 工具能够交付什么。每张卡片、图表、滑块、对话框和轮播都是 Python 组件。像这样构建组合，加上 @mcp.tool(app=True)，宿主就会在对话中渲染它。
在底层，FastMCP 基于 MCP Apps extension，并使用 Prefab 在 Python 中描述 UI。
pip install "fastmcp[apps]"

Prefab 正在活跃开发中，且经常出现破坏性变更。FastMCP 会设置最低 prefab-ui 版本，但不会固定上限；部署前请在你自己的依赖中 将 prefab-ui 固定到特定版本。

选择你的路径
四种模式几乎覆盖你想构建的一切。大多数 app 都从 Interactive Tools 开始；只有遇到特定限制时，才需要使用其他模式。

Interactive Tools — 从这里开始
给工具添加 app=True 并返回 Prefab 组件。图表、表格、dashboard，以及客户端侧交互（开关、标签页、过滤）都可以工作，无需任何服务端往返。
@mcp.tool(app=True)
def team_directory() -> DataTable:
    return DataTable(columns=[...], rows=employees, search=True)

FastMCPApp — 当 UI 需要回调服务端时
保存数据的表单、触发后端工作的按钮、查询数据库的搜索。FastMCPApp 管理 UI 动作和后端工具之间的连接，并提供稳定的工具标识符，即使经过服务端组合也能保持可用。

Generative UI — 当 LLM 编写 UI 时
注册一个 provider，模型就可以根据当前数据和请求编写定制的 Prefab 代码。用户会看到 UI 随着模型生成过程逐步构建出来。
mcp.add_provider(GenerativeUI())

Custom HTML — 当你需要完全控制时
编写你自己的 HTML、CSS 和 JavaScript。使用特定框架，嵌入地图或 3D 查看器，嵌入视频。此时你是在直接和 MCP Apps 协议交互。

接下来
快速开始 — 一分钟构建一个可工作的 app
示例 — 可以立即运行的完整服务端
Providers — 用一行代码添加现成能力（审批、选择器、文件上传、表单）
开发 — 使用 fastmcp dev apps 在本地预览 app 工具
OpenTelemetry

快速开始

x

