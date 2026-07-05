---
title: "SDK: Utilities (UI, Version, CLI, Config)"
source: "https://fastmcp.wiki/zh/python-sdk/fastmcp-utilities-ui"
version: "latest"
---

# SDK: Utilities (UI, Version, CLI, Config)

> 原始文档来源：https://fastmcp.wiki/zh/python-sdk/fastmcp-utilities-ui (Python SDK API Reference)

---

fastmcp.utilities.ui
Functions
create_page 
create_logo 
create_status_message 
create_info_box 
create_detail_box 
create_button_group 
create_secure_html_response 
fastmcp.utilities
ui

fastmcp.utilities.ui
FastMCP HTML 页面的共享 UI 工具。
此模块为 OAuth 回调、同意页面和其他面向用户的界面提供可复用的 HTML/CSS 组件。

Functions

create_page 
create_page(content: str, title: str = 'FastMCP', additional_styles: str = '', csp_policy: str = "default-src 'none'; style-src 'unsafe-inline'; img-src https: data:; base-uri 'none'") -> str

创建带 FastMCP 样式的完整 HTML 页面。
参数：
content：要放入页面内的 HTML 内容
title：页面标题
additional_styles：要包含的额外 CSS
csp_policy：Content Security Policy 头值。如果为空字符串 ""，则完全省略 CSP meta 标签。
返回：
字符串形式的完整 HTML 页面

create_logo 
create_logo(icon_url: str | None = None, alt_text: str = 'FastMCP') -> str

创建 logo HTML。
参数：
icon_url：可选的自定义图标 URL。未提供时使用 FastMCP logo。
alt_text：logo 图片的 alt 文本。
返回：
logo 图片标签的 HTML。

create_status_message 
create_status_message(message: str, is_success: bool = True) -> str

创建带图标的状态消息。
参数：
message：状态消息文本
is_success：True 表示成功（✓），False 表示错误（✕）
返回：
状态消息的 HTML

create_info_box 
create_info_box(content: str, is_error: bool = False, centered: bool = False, monospace: bool = False) -> str

创建信息框。
参数：
content：信息框的 HTML 内容
is_error：True 表示错误样式，False 表示普通样式
centered：True 表示文本居中，False 表示左对齐
monospace：True 表示使用灰色等宽字体样式，而不是蓝色样式
返回：
信息框的 HTML

create_detail_box 
create_detail_box(rows: list[tuple[str, str]]) -> str

创建包含键值对的详情框。
参数：
rows：(label, value) 元组列表
返回：
详情框的 HTML

create_button_group 
create_button_group(buttons: list[tuple[str, str, str]]) -> str

创建一组按钮。
参数：
buttons：(text, value, css_class) 元组列表
返回：
按钮组的 HTML

create_secure_html_response 
create_secure_html_response(html: str, status_code: int = 200) -> HTMLResponse

创建带安全头的 HTMLResponse。
根据 MCP 安全最佳实践，添加 X-Frame-Options: DENY 以防止点击劫持攻击。
参数：
html：要返回的 HTML 内容
status_code：HTTP 状态码
返回：
带安全头的 HTMLResponse
types
version_check

fastmcp.utilities.version_check
函数
get_latest_version 
check_for_newer_version 
fastmcp.utilities
version_check

fastmcp.utilities.version_check
FastMCP 的版本检查实用工具。

函数

get_latest_version 
get_latest_version(include_prereleases: bool = False) -> str | None

从 PyPI 获取 FastMCP 最新版本，并在可用时使用缓存。
参数：
include_prereleases: 如果为 True，则包含预发布版本。
返回：
最新版本字符串；如果不可用，则返回 None。

check_for_newer_version 
check_for_newer_version() -> str | None

检查是否有更新版本的 FastMCP 可用。
返回：
如果有比当前版本更新的版本，则返回最新版本字符串；否则返回 None。
ui
versions

fastmcp.utilities.versions
函数
parse_version_key 
version_sort_key 
compare_versions 
is_version_greater 
max_version 
min_version 
dedupe_with_versions 
类
VersionSpec 
matches 
intersect 
VersionKey 
fastmcp.utilities
versions

fastmcp.utilities.versions
用于组件版本管理的版本比较工具。
此模块提供比较组件版本的工具。版本会先尝试按 PEP 440 版本解析（使用 packaging 库），如果无法解析，则回退到按字典序比较字符串。
示例：
“1”、“2”、“10” → 按 PEP 440 解析，并按语义比较（1 < 2 < 10）
“1.0”、“2.0” → 按 PEP 440 解析
“v1.0” → 去掉 v 前缀，按 “1.0” 解析
“2025-01-15” → 不是有效的 PEP 440，按字符串比较
None → 排序最低（无版本组件）

函数

parse_version_key 
parse_version_key(version: str | None) -> VersionKey

将版本字符串解析为可排序的键。
参数：
version：版本字符串；如果为 None，则表示无版本。
返回：
适合排序的 VersionKey。

version_sort_key 
version_sort_key(component: FastMCPComponent) -> tuple[VersionKey, str]

根据组件版本获取排序键。
可与 sorted() 或 max() 一起使用，按版本对组件排序。
该键是 (VersionKey, raw) 元组。VersionKey 按 PEP 440 语义排序（或对非 PEP 440 字符串按字典序排序）；原始版本字符串是确定性的平局决胜项，因此两个在 PEP 440 下等价但写法不同的版本（例如 "1" 和 "1.0"）会以可复现方式排序，而不是依赖注册顺序。原始平局决胜项只影响等价版本之间的平局，绝不会影响主要版本顺序，因此范围/相等匹配（直接使用 VersionKey）保持不变。
参数：
component：要获取排序键的组件。
返回：
一个确定性、可排序的 (VersionKey, raw) 元组。

compare_versions 
compare_versions(a: str | None, b: str | None) -> int

比较两个版本字符串。
参数：
a：第一个版本字符串（或 None）。
b：第二个版本字符串（或 None）。
返回：
如果 a < b，则为 -1；如果 a == b，则为 0；如果 a > b，则为 1。

is_version_greater 
is_version_greater(a: str | None, b: str | None) -> bool

检查版本 a 是否大于版本 b。
参数：
a：第一个版本字符串（或 None）。
b：第二个版本字符串（或 None）。
返回：
如果 a > b，则为 True；否则为 False。

max_version 
max_version(a: str | None, b: str | None) -> str | None

返回两个版本中较大的那个。
参数：
a：第一个版本字符串（或 None）。
b：第二个版本字符串（或 None）。
返回：
较大的版本；如果两者都是 None，则为 None。

min_version 
min_version(a: str | None, b: str | None) -> str | None

返回两个版本中较小的那个。
参数：
a：第一个版本字符串（或 None）。
b：第二个版本字符串（或 None）。
返回：
较小的版本；如果两者都是 None，则为 None。

dedupe_with_versions 
dedupe_with_versions(components: Sequence[C], key_fn: Callable[[C], str]) -> list[C]

按键对组件去重，并保留最高版本。
按键对组件分组，从每组中选择最高版本；如果任一组件带版本，则把可用版本注入 meta。
参数：
components：组件序列。
key_fn：从组件中提取分组键的函数。
返回：
已去重并注入版本信息到 meta 的列表。

类

VersionSpec 
用于按版本过滤组件的规范。
转换和提供方会使用它将组件过滤到特定版本或版本范围。无版本组件（version=None）总是匹配任意规范。
参数：
gte：如果设置，仅匹配大于等于此值的版本。
lt：如果设置，仅匹配小于此值的版本。
eq：如果设置，仅匹配此精确版本（忽略 gte/lt）。 匹配会按 PEP 440 规范化，并且对 v 前缀不敏感，因此 eq="v1.0" 会匹配版本为 "1.0" 的组件，eq="1.0" 也会匹配 "1"（PEP 440 将 1 和 1.0 视为相同版本）。如果服务端注册了同一组件的两个 PEP 440 等价写法（例如同时注册 "1" 和 "1.0"），它们在此规范下属于同一版本；选择其中哪个是确定性的（参见 version_sort_key），不依赖注册顺序。
方法：

matches 
matches(self, version: str | None) -> bool

检查某个版本是否匹配此规范。
参数：
version：要检查的版本；如果为 None，则表示无版本。
match_none：无版本（None）组件是否匹配。为了与检索操作保持向后兼容，默认值为 True。在进行过滤（例如启用/禁用）时可设为 False，以便从特定版本规则中排除无版本组件。
返回：
如果版本匹配此规范，则为 True。

intersect 
intersect(self, other: VersionSpec | None) -> VersionSpec

返回同时满足此规范和另一规范的规范。
转换会使用它把调用方约束与过滤约束组合起来。例如，如果 VersionFilter 具有 lt=“3.0”，而调用方请求 eq=“1.0”，交集会验证 “1.0” 位于范围内，并返回精确规范。
参数：
other：要与之求交的另一个规范，或 None。
返回：
一个只匹配同时满足两个规范的版本的 VersionSpec。

VersionKey 
可比较的版本键，支持 None、PEP 440 版本和字符串。
比较顺序：
None（无版本）排序最低
PEP 440 版本按语义版本顺序排序
无效版本（字符串）按字典序排序
比较 PEP 440 与字符串时，PEP 440 排在前面
version_check

fastmcp.utilities.cli
函数
is_already_in_uv_subprocess 
load_and_merge_config 
log_server_banner 
fastmcp.utilities
cli

fastmcp.utilities.cli

函数

is_already_in_uv_subprocess 
is_already_in_uv_subprocess() -> bool

检查当前是否已经在 FastMCP uv 子进程中运行。

load_and_merge_config 
load_and_merge_config(server_spec: str | None, **cli_overrides) -> tuple[MCPServerConfig, str]

从 server_spec 加载配置，并应用 CLI 覆盖项。
这会整合 run、inspect 和 dev 命令中重复的配置解析逻辑。
参数：
server_spec: Python 文件、配置文件、URL，或用于自动检测的 None
cli_overrides: 覆盖配置值的 CLI 参数
返回：
(MCPServerConfig, resolved_server_spec) 元组

log_server_banner 
log_server_banner(server: FastMCP[Any]) -> None

创建并记录包含服务端信息和 logo 的格式化横幅。
authorization
components

fastmcp.utilities.mcp_server_config
mcp_server_config
__init__

fastmcp.utilities.mcp_server_config
FastMCP 配置模块。
此模块为 FastMCP 服务端提供带版本的配置支持。 当前版本是 v1，并在此处重新导出以便使用。
logging
__init__

fastmcp.utilities.mcp_server_config.v1.mcp_server_config
函数
generate_schema 
类
Deployment 
apply_runtime_settings 
MCPServerConfig 
validate_source 
validate_environment 
validate_deployment 
from_file 
from_cli_args 
find_config 
prepare 
prepare_environment 
prepare_source 
run_server 
v1
mcp_server_config

fastmcp.utilities.mcp_server_config.v1.mcp_server_config
FastMCP 配置文件支持。
此模块为 fastmcp.json 配置文件提供支持，允许 用户以声明式格式指定服务器设置，而不使用 命令行参数。

函数

generate_schema 
generate_schema(output_path: Path | str | None = None) -> dict[str, Any] | None

为 fastmcp.json 文件生成 JSON 模式。
这用于创建 IDE 可用于 验证和自动补全的模式文件。
参数：
output_path: 写入模式的可选路径。如果提供， 则写入模式并返回 None。如果未提供， 则将模式作为字典返回。
返回值：
如果 output_path 为 None，则返回字典形式的 JSON 模式，否则返回 None

类

Deployment 
服务器部署和运行时设置的配置。
方法：

apply_runtime_settings 
apply_runtime_settings(self, config_path: Path | None = None) -> None

应用运行时设置，如环境变量和工作目录。
参数：
config_path: 用于解析相对路径的配置文件路径
环境变量支持使用 
𝑉
𝐴
𝑅
𝑁
𝐴
𝑀
𝐸
语法进行插值。例如：
"
𝐴
𝑃
𝐼
𝑈
𝑅
𝐿
"
:
"
ℎ
𝑡
𝑡
𝑝
𝑠
:
/
/
𝑎
𝑝
𝑖
.
VAR
N
	

AME语法进行插值。例如："API
U
	

RL":"https://api..example.com” 将在运行时替换 ENVIRONMENT 变量的值。

MCPServerConfig 
FastMCP 服务器的配置。
此配置文件允许您以声明式格式指定运行 FastMCP 服务器所需的所有设置。
方法：

validate_source 
validate_source(cls, v: dict | Source) -> SourceType

验证并将源转换为正确的格式。
支持：
字典格式：{"path": "server.py", "entrypoint": "app"}
FileSystemSource 实例（直接传递）
这里不会发生字符串解析 - 这只在 CLI 边界发生。 MCPServerConfig 只使用正确类型的对象。

validate_environment 
validate_environment(cls, v: dict | Any) -> EnvironmentType

确保环境具有用于区分的类型字段。
为了向后兼容，如果未指定类型，则默认为 “uv”。

validate_deployment 
validate_deployment(cls, v: dict | Deployment) -> Deployment

验证并将部署转换为 Deployment。
接受：
Deployment 实例
可转换为 Deployment 的字典

from_file 
from_file(cls, file_path: Path) -> MCPServerConfig

从 JSON 文件加载配置。
参数：
file_path: 配置文件的路径
返回值：
MCPServerConfig 实例
引发异常：
FileNotFoundError: 如果文件不存在
json.JSONDecodeError: 如果文件不是有效的 JSON
pydantic.ValidationError: 如果配置无效

from_cli_args 
from_cli_args(cls, source: FileSystemSource, transport: Literal['stdio', 'http', 'sse', 'streamable-http'] | None = None, host: str | None = None, port: int | None = None, path: str | None = None, log_level: Literal['DEBUG', 'INFO', 'WARNING', 'ERROR', 'CRITICAL'] | None = None, python: str | None = None, dependencies: list[str] | None = None, requirements: str | None = None, project: str | None = None, editable: str | None = None, env: dict[str, str] | None = None, cwd: str | None = None, args: list[str] | None = None) -> MCPServerConfig

从 CLI 参数创建配置。
这允许我们有一个单一的代码路径，其中所有内容 都通过配置对象。
参数：
source: 服务器源（FileSystemSource 实例）
transport: 传输协议
host: HTTP 传输的主机
port: HTTP 传输的端口
path: 服务器的 URL 路径
log_level: 日志级别
python: Python 版本
dependencies: 要安装的 Python 包
requirements: requirements 文件的路径
project: 项目目录的路径
editable: 以可编辑模式安装的路径
env: 环境变量
cwd: 工作目录
args: 服务器参数
返回值：
MCPServerConfig 实例

find_config 
find_config(cls, start_path: Path | None = None) -> Path | None

在指定目录中查找 fastmcp.json 文件。
参数：
start_path: 要查找的目录（默认为当前目录）
返回值：
配置文件的路径，如果未找到则为 None

prepare 
prepare(self, skip_source: bool = False, output_dir: Path | None = None) -> None

为执行准备环境和源。
当提供 output_dir 时，创建一个持久化的 uv 项目。 当 output_dir 为 None 时，执行临时缓存（为了向后兼容）。
参数：
skip_source: 如果为 True，则跳过源准备
output_dir: 在其中创建持久化 uv 项目的目录（可选）

prepare_environment 
prepare_environment(self, output_dir: Path | None = None) -> None

准备 Python 环境。
参数：
output_dir: 如果提供，在此目录中创建持久化的 uv 项目。 如果为 None，则只填充 uv 的缓存以供临时使用。
委托给环境的 prepare() 方法

prepare_source 
prepare_source(self) -> None

为加载准备源。
委托给源的 prepare() 方法。

run_server 
run_server(self, **kwargs: Any) -> None

使用此配置加载并运行服务器。
参数：
**kwargs: 传递给 server.run_async() 的附加参数 这些参数会覆盖配置设置
uv
__init__

