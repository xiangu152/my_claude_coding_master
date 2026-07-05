---
title: "应用示例"
source: "https://fastmcp.wiki/zh/apps/examples"
version: "latest"
---

# 应用示例

> 原始文档来源：https://fastmcp.wiki/zh/apps/examples

---

运行示例
独立应用
销售仪表盘
系统监控器
测验
交互式地图
参考
示例

可以立即运行的示例应用。

版本
3.2.0
新增
下面每个卡片都是一个可工作的 FastMCP 服务端，你可以用 fastmcp dev apps 运行，也可以从任意 MCP 宿主连接。源码位于仓库的 examples/apps/ 中。

销售仪表盘

指标、图表和交易管道

系统监控器

实时 CPU、内存、磁盘，并自动刷新

测验

LLM 生成的问答题，带计分功能

交互式地图

在 Leaflet 上展示地理编码后的地址

文件上传

拖放上传 provider

审批

人在回路的确认流程

选择

可点击的选项选择

表单输入

Pydantic 模型表单

生成式 UI

LLM 在运行时编写 UI

运行示例
使用开发服务端在浏览器中预览任意示例：
pip install "fastmcp[apps]"
fastmcp dev apps examples/apps/sales_dashboard/sales_dashboard_server.py

开发 UI 可以让你选择工具并填写参数。在真实部署中，LLM 会根据对话上下文提供这些参数。测验示例在连接到 Goose 或 Claude Desktop 这类宿主时尤其亮眼，因为问题由 LLM 自己生成。

独立应用

销售仪表盘
一个完整的仪表盘，包含 KPI 指标、收入趋势、分段拆解和交易管道表。它展示了只用一个 app=True 工具以及 Prefab 的图表和数据组件可以构建什么。
fastmcp dev apps examples/apps/sales_dashboard/sales_dashboard_server.py

系统监控器
使用 psutil 从你的机器读取实时 CPU、内存和磁盘统计信息。通过 SetInterval 调用后端工具实现自动刷新，并提供下拉菜单控制刷新频率。图表会随时间最多累积 100 个数据点。
pip install psutil
fastmcp dev apps examples/apps/system_monitor/system_monitor_server.py

测验
LLM 生成问答题并将它们传给工具。用户通过按钮回答，查看正确/错误反馈，并在多个问题之间跟踪分数。该示例演示了使用 FastMCPApp 管理多轮客户端状态。
fastmcp dev apps examples/apps/quiz/quiz_server.py

交互式地图
接受地址或地点名称，通过 OpenStreetMap Nominatim（免费，无需 API key）进行地理编码，并使用 Prefab 的 Embed 组件和内联 HTML 渲染交互式 Leaflet 地图。这也提醒我们：Prefab 应用在需要时可以跳出内置组件的范围。
fastmcp dev apps examples/apps/map/map_server.py

如需审批、选择器、文件上传和 Pydantic 表单等现成构建块，请参阅 Providers 分组。
开发

架构

x

