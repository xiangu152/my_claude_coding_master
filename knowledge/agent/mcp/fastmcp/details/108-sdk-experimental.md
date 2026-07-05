---
title: "SDK: Experimental (Sampling, Transforms, Code Mode)"
source: "https://fastmcp.wiki/zh/python-sdk/fastmcp-experimental-__init__"
version: "latest"
---

# SDK: Experimental (Sampling, Transforms, Code Mode)

> 原始文档来源：https://fastmcp.wiki/zh/python-sdk/fastmcp-experimental-__init__ (Python SDK API Reference)

---

fastmcp.experimental
fastmcp.experimental
__init__

fastmcp.experimental
此模块为空，或仅包含私有/内部实现。
generative
__init__

fastmcp.experimental.sampling
sampling
__init__

fastmcp.experimental.sampling
此模块为空，或仅包含私有/内部实现。
__init__
handlers

fastmcp.experimental.sampling.handlers
sampling
handlers

fastmcp.experimental.sampling.handlers
此模块为空，或仅包含私有/内部实现。
__init__
__init__

fastmcp.experimental.transforms
transforms
__init__

fastmcp.experimental.transforms
此模块为空，或仅包含私有/内部实现。
handlers
code_mode

fastmcp.experimental.transforms.code_mode
Classes
SandboxProvider 
run 
MontySandboxProvider 
run 
Search 
GetSchemas 
GetTags 
ListTools 
CodeMode 
transform_tools 
get_tool 
transforms
code_mode

fastmcp.experimental.transforms.code_mode

Classes

SandboxProvider 
用于在沙箱中执行 LLM 生成的 Python 代码的接口。
警告：传给 run 的 code 参数包含不可信的、由 LLM 生成的 Python。实现必须在隔离沙箱中执行它，绝不能直接使用 exec()。生产工作负载请使用由 pydantic-monty 支撑的 MontySandboxProvider。
Methods:

run 
run(self, code: str) -> Any

MontySandboxProvider 
由 pydantic-monty 支撑的沙箱提供器。
参数：
limits：沙箱执行的资源限制。支持的 key\： max_duration_secs (float), max_allocations (int), max_memory (int), max_recursion_depth (int), gc_interval (int)。所有 key 都是可选的；省略某个 key 表示不限制该项。
完全省略该参数时，会应用保守基线（max_duration_secs=30、max_memory=100 MB），确保开箱配置不是无限制的。传入 limits=None 可显式无任何限制地运行，或传入字典以设置自己的限制。
Methods:

run 
run(self, code: str) -> Any

Search 
按查询搜索目录的发现工具工厂。
参数：
search_fn：异步可调用对象 (tools, query) -> matching_tools。默认使用 BM25 排名。
name：暴露给 LLM 的合成工具名称。
default_detail：搜索结果的默认详情级别。"brief" 仅返回工具名称和描述。"detailed" 返回包含参数 schema 的紧凑 markdown。"full" 返回完整 JSON 工具定义。
default_limit：要返回的最大结果数量。LLM 可以在每次调用时覆盖它。None 表示无限制。

GetSchemas 
按名称返回工具 schema 的发现工具工厂。
参数：
name：暴露给 LLM 的合成工具名称。
default_detail：schema 结果的默认详情级别。"brief" 仅返回工具名称和描述。"detailed" 渲染包含参数名称、类型和必需标记的紧凑 markdown。"full" 返回完整 JSON schema。

GetTags 
列出目录中工具标签的发现工具工厂。
从目录中读取 tool.tags，并按标签对工具分组。没有标签的工具会出现在 "untagged" 下。
参数：
name：暴露给 LLM 的合成工具名称。
default_detail：默认详情级别。"brief" 返回标签名称和工具数量。"full" 列出每个标签下的所有工具。

ListTools 
列出目录中所有工具的发现工具工厂。
参数：
name：暴露给 LLM 的合成工具名称。
default_detail：默认详情级别。"brief" 返回工具名称和单行描述。"detailed" 返回包含参数 schema 的紧凑 markdown。"full" 返回完整 JSON schema。

CodeMode 
将所有工具折叠为“发现 + 执行”元工具的转换。
发现工具可通过 discovery_tools 参数组合。每个发现工具都是一个可调用对象，接收目录访问能力并返回 Tool。默认包含 Search 和 GetSchemas 以支持渐进披露：search 查找候选项，get_schema 获取参数详情，execute 运行代码。
execute 工具始终存在，并提供一个沙箱化 Python 环境，其中作用域内包含 call_tool(name, params)。
Methods:

transform_tools 
transform_tools(self, tools: Sequence[Tool]) -> Sequence[Tool]

get_tool 
get_tool(self, name: str, call_next: GetToolNext) -> Tool | None

__init__
__init__

