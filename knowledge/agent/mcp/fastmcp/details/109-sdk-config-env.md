---
title: "SDK: Config Environments & Sources"
source: "https://fastmcp.wiki/zh/python-sdk/fastmcp-utilities-mcp_server_config-v1-environments-__init__"
version: "latest"
---

# SDK: Config Environments & Sources

> 原始文档来源：https://fastmcp.wiki/zh/python-sdk/fastmcp-utilities-mcp_server_config-v1-environments-__init__ (Python SDK API Reference)

---

fastmcp.utilities.mcp_server_config.v1.environments
environments
__init__

fastmcp.utilities.mcp_server_config.v1.environments
MCP 服务端的环境配置。
__init__
base

fastmcp.utilities.mcp_server_config.v1.environments.base
类
Environment 
build_command 
prepare 
environments
base

fastmcp.utilities.mcp_server_config.v1.environments.base

类

Environment 
环境配置的基类。
方法：

build_command 
build_command(self, command: list[str]) -> list[str]

构建带环境设置的完整命令。
参数：
command: 要用环境设置包装的基础命令
返回：
可供 subprocess 执行的完整命令

prepare 
prepare(self, output_dir: Path | None = None) -> None

准备环境（可选，也可以不执行任何操作）。
参数：
output_dir: 持久化环境设置所用的目录
__init__
uv

fastmcp.utilities.mcp_server_config.v1.environments.uv
类
UVEnvironment 
build_command 
prepare 
environments
uv

fastmcp.utilities.mcp_server_config.v1.environments.uv

类

UVEnvironment 
Python 环境设置的配置。
方法：

build_command 
build_command(self, command: list[str]) -> list[str]

构建完整的 uv run 命令，包含环境参数和要执行的命令。
参数：
command: 要执行的命令（例如 [“fastmcp”, “run”, “server.py”]）
返回：
可直接传给 subprocess.run 的完整命令，必要时包含 “uv” 前缀。
如果未设置环境配置，则原样返回命令。

prepare 
prepare(self, output_dir: Path | None = None) -> None

使用 uv 准备 Python 环境。
参数：
output_dir: 创建持久化 uv 项目的目录。如果为 None，则创建临时目录供一次性使用。
base
mcp_server_config

fastmcp.utilities.mcp_server_config.v1.sources
sources
__init__

fastmcp.utilities.mcp_server_config.v1.sources
此模块为空，或仅包含私有/内部实现。
mcp_server_config
base

fastmcp.utilities.mcp_server_config.v1.sources.base
类
Source 
prepare 
load_server 
sources
base

fastmcp.utilities.mcp_server_config.v1.sources.base

类

Source 
所有来源类型的抽象基类。
方法：

prepare 
prepare(self) -> None

准备来源（下载、克隆、安装等）。
对于需要准备的来源（例如 git clone、下载），此方法会执行相应准备。对于不需要准备的来源（例如本地文件），此方法不执行任何操作。

load_server 
load_server(self) -> Any

加载并返回 FastMCP 服务端实例。
如果来源需要准备，则必须在 prepare() 之后调用。加载服务端所需的所有信息都应作为来源实例上的属性可用。
__init__
filesystem

fastmcp.utilities.mcp_server_config.v1.sources.filesystem
类
FileSystemSource 
parse_path_with_object 
load_server 
sources
filesystem

fastmcp.utilities.mcp_server_config.v1.sources.filesystem

类

FileSystemSource 
本地 Python 文件的来源。
方法：

parse_path_with_object 
parse_path_with_object(cls, v: str) -> str

解析 path:object 语法并提取对象名称。
此验证器会在模型创建前运行，让我们可以在模型边界处理 “file.py:object” 语法。

load_server 
load_server(self) -> Any

从文件系统加载服务端。
base
mime

fastmcp.utilities
fastmcp.utilities
__init__

fastmcp.utilities
FastMCP 实用工具模块。
code_mode
async_utils

