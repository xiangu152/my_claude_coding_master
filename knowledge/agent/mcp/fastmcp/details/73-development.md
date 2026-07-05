---
title: "应用开发"
source: "https://fastmcp.wiki/zh/apps/development"
version: "latest"
---

# 应用开发

> 原始文档来源：https://fastmcp.wiki/zh/apps/development

---

快速开始
工作原理
MCP 检查器
选项
多个工具
参考
开发

无需完整 MCP 宿主，即可在本地预览和测试应用工具。

版本
3.2.0
新增
fastmcp dev apps 可以在不需要 MCP 宿主客户端的情况下，为你的应用工具提供浏览器预览。它会同时启动你的服务端和本地开发 UI：你选择一个工具，填写参数，渲染后的结果会在新标签页中打开。
它同时适用于交互式工具和自定义 HTML 应用。

快速开始
fastmcp dev apps server.py

开发 UI 会在 http://localhost:8080 打开。你的 MCP 服务端运行在 8000 端口，并默认启用自动重载：保存文件后，服务端会自动重启。

工作原理
开发服务端会做三件事：
选择页面会连接到你的 MCP 服务端，找到所有带 UI 元数据的工具，并为每个工具渲染一个表单。表单会根据工具的输入 schema 自动生成，包括文本字段、下拉菜单、复选框，并且都已经接好调用逻辑。
提交表单时，开发服务端会通过 MCP 协议调用你的工具，并在新标签页中打开结果。结果页面会在 AppBridge 内加载工具的 UI 资源（Prefab 渲染器或你的自定义 HTML），这与真实 MCP 宿主使用的是同一套协议。
/mcp 上的反向代理会把浏览器请求转发给你的 MCP 服务端，避免 CORS 问题。否则，基于 iframe 的渲染器在与不同端口通信时会被浏览器阻止。

MCP 检查器
开发 UI 左侧包含一个检查器面板，可以实时捕获 MCP 流量。它会显示浏览器和服务端之间流动的 JSON-RPC 消息，包括请求、响应以及 AppBridge postMessage 流量。
每个条目都会显示方向、方法、耗时和智能摘要。点击任意条目即可展开完整的 JSON-RPC 正文。除非你已经向上滚动查看旧消息，否则面板会自动滚动到新消息。
检查器很适合调试：你可以准确看到工具收到的参数、返回的内容，以及 AppBridge 如何与渲染器通信。

选项
fastmcp dev apps server.py:mcp --mcp-port 9000 --dev-port 9090 --no-reload

选项	标志	默认值	描述
MCP 端口	--mcp-port	8000	MCP 服务端使用的端口
开发端口	--dev-port	8080	开发 UI 使用的端口
自动重载	--reload / --no-reload	开启	监听文件并在变更时重启服务端

多个工具
如果服务端有多个应用工具，选择器会显示一个下拉菜单。每个工具都有自己的表单和启动按钮。如果工具提供了 title，会显示该标题；否则回退到工具名称。
# 带有多个应用工具的服务端
fastmcp dev apps examples/apps/contacts/contacts_server.py

表单输入

示例

x

