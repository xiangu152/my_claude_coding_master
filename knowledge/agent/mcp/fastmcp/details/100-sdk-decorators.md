---
title: "SDK: Decorators & Dependencies"
source: "https://fastmcp.wiki/zh/python-sdk/fastmcp-decorators"
version: "latest"
---

# SDK: Decorators & Dependencies

> 原始文档来源：https://fastmcp.wiki/zh/python-sdk/fastmcp-decorators (Python SDK API Reference)

---

fastmcp.decorators
函数
resolve_task_config 
get_fastmcp_meta 
类
HasFastMCPMeta 
decorators

fastmcp.decorators
FastMCP 的共享装饰器实用工具。

函数

resolve_task_config 
resolve_task_config(task: bool | TaskConfig | None) -> bool | TaskConfig

解析任务配置，并将 None 默认视为 False。

get_fastmcp_meta 
get_fastmcp_meta(fn: Any) -> Any | None

从函数中提取 FastMCP 元数据，同时处理绑定方法和包装器。

类

HasFastMCPMeta 
带有 FastMCP 元数据装饰的可调用对象协议。
client
dependencies

fastmcp.dependencies
dependencies

fastmcp.dependencies
FastMCP 的依赖注入导出。
此模块重新导出依赖注入符号，为所有依赖相关功能提供一个清晰、集中的导入位置。
DI 功能（Depends、CurrentContext、CurrentFastMCP）无需 pydocket，使用 uncalled-for DI 引擎即可工作。只有任务相关依赖（CurrentDocket、CurrentWorker）和后台任务执行需要 fastmcp[tasks]。
decorators
exceptions

