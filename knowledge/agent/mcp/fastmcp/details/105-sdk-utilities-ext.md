---
title: "SDK: Utilities (OpenAPI, Pagination, Skills, Tasks)"
source: "https://fastmcp.wiki/zh/python-sdk/fastmcp-utilities-openapi"
version: "latest"
---

# SDK: Utilities (OpenAPI, Pagination, Skills, Tasks)

> 原始文档来源：https://fastmcp.wiki/zh/python-sdk/fastmcp-utilities-openapi (Python SDK API Reference)

---

fastmcp.utilities.openapi
fastmcp.utilities
openapi

fastmcp.utilities.openapi
FastMCP 的 OpenAPI 实用工具，已重构以提升可维护性。
mime
pagination

fastmcp.utilities.pagination
函数
paginate_sequence 
类
CursorState 
encode 
decode 
fastmcp.utilities
pagination

fastmcp.utilities.pagination
MCP 列表操作的分页实用工具。

函数

paginate_sequence 
paginate_sequence(items: Sequence[T], cursor: str | None, page_size: int) -> tuple[list[T], str | None]

对条目序列进行分页。
参数：
items: 要分页的完整序列。
cursor: 来自上一次请求的可选游标。第一页使用 None。
page_size: 每页最大条目数。
返回：
(page_items, next_cursor) 元组。如果没有更多页面，next_cursor 为 None。
抛出：
ValueError: 如果游标无效。

类

CursorState 
分页游标状态的内部表示。
游标会编码结果集中的偏移量。按照 MCP 规范，这对客户端是不透明的；客户端不应解析或修改游标。
方法：

encode 
encode(self) -> str

将游标状态编码为不透明字符串。

decode 
decode(cls, cursor: str) -> CursorState

从不透明字符串解码游标。
抛出：
ValueError: 如果游标无效或格式错误。
openapi
skills

fastmcp.utilities.skills
函数
list_skills 
get_skill_manifest 
download_skill 
sync_skills 
类
SkillSummary 
SkillFile 
SkillManifest 
fastmcp.utilities
skills

fastmcp.utilities.skills
用于从 MCP 服务端发现和下载 skills 的客户端实用工具。

函数

list_skills 
list_skills(client: Client) -> list[SkillSummary]

列出 MCP 服务端中的所有可用 skills。
通过查找 URI 匹配 skill://{name}/SKILL.md 模式的资源来发现 skills。
参数：
client: 已连接的 FastMCP 客户端
返回：
包含名称、描述和 URI 的 SkillSummary 对象列表

get_skill_manifest 
get_skill_manifest(client: Client, skill_name: str) -> SkillManifest

获取特定 skill 的 manifest。
参数：
client: 已连接的 FastMCP 客户端
skill_name: skill 名称
返回：
包含文件列表的 SkillManifest
抛出：
ValueError: 如果无法读取或解析 manifest

download_skill 
download_skill(client: Client, skill_name: str, target_dir: str | Path) -> Path

将一个 skill 及其所有文件下载到本地目录。
创建一个以 skill 命名的子目录，其中包含所有文件。
参数：
client: 已连接的 FastMCP 客户端
skill_name: 要下载的 skill 名称
target_dir: 创建 skill 文件夹的目录
overwrite: 如果为 True，则覆盖现有 skill 目录。如果为 False（默认），且目录已存在，则抛出 FileExistsError。
返回：
已下载 skill 目录的路径
抛出：
ValueError: 如果无法找到或下载 skill
FileExistsError: 如果 skill 目录已存在且 overwrite=False

sync_skills 
sync_skills(client: Client, target_dir: str | Path) -> list[Path]

从服务端下载所有可用 skills。
参数：
client: 已连接的 FastMCP 客户端
target_dir: 创建 skill 文件夹的目录
overwrite: 如果为 True，则覆盖现有文件
返回：
已下载 skill 目录的路径列表

类

SkillSummary 
服务端上可用 skill 的摘要信息。

SkillFile 
skill 中某个文件的信息。

SkillManifest 
包含所有文件的 skill 完整 manifest。
pagination
tasks

fastmcp.utilities.tasks
类
TaskMeta 
TaskConfig 
from_bool 
supports_tasks 
validate_function 
fastmcp.utilities
tasks

fastmcp.utilities.tasks
FastMCP 组件的任务配置基础类型。

类

TaskMeta 
任务增强执行请求的元数据。
属性：
ttl: 客户端请求的 TTL，单位为毫秒。如果为 None，则使用服务端默认值。
fn_key: Docket 路由键。如果为 None，则从组件名称自动派生。

TaskConfig 
MCP 后台任务执行的配置。
控制组件如何处理任务增强请求：
forbidden：组件不支持任务执行。
optional：组件同时支持同步执行和任务执行。
required：组件要求使用任务执行。
方法：

from_bool 
from_bool(cls, value: bool) -> TaskConfig

将布尔任务标志转换为 TaskConfig。

supports_tasks 
supports_tasks(self) -> bool

检查此组件是否支持任务执行。

validate_function 
validate_function(self, fn: Callable[..., Any], name: str) -> None

验证函数是否与此任务配置兼容。
skills
tests

fastmcp.utilities.tests
函数
temporary_settings 
run_server_in_process 
run_server_async 
类
HeadlessOAuth 
redirect_handler 
callback_handler 
fastmcp.utilities
tests

fastmcp.utilities.tests

函数

temporary_settings 
temporary_settings(**kwargs: Any)

临时覆盖 FastMCP 设置值。
参数：
**kwargs: 要覆盖的设置，包括嵌套设置。

run_server_in_process 
run_server_in_process(server_fn: Callable[..., None], *args: Any, **kwargs: Any) -> Generator[str, None, None]

在单独进程中运行 FastMCP 服务端并返回服务端 URL 的上下文管理器。退出上下文管理器时，服务端进程会被终止。
参数：
server_fn: 运行 FastMCP 服务端的函数。FastMCP 服务端不可 pickle，因此需要一个创建并运行服务端的函数。
*args: 传递给服务端函数的参数。
provide_host_and_port: 是否将 host 和 port 作为 kwargs 提供给服务端函数。
host: 服务端绑定的 host（默认: “127.0.0.1”）。
port: 服务端绑定的端口（默认: 查找可用端口）。
**kwargs: 传递给服务端函数的关键字参数。
返回：
服务端 URL。

run_server_async 
run_server_async(server: FastMCP, port: int | None = None, transport: Literal['http', 'streamable-http', 'sse'] = 'http', path: str = '/mcp', host: str = '127.0.0.1') -> AsyncGenerator[str, None]

将 FastMCP 服务端作为 asyncio task 启动，用于进程内异步测试。
这是测试 FastMCP 服务端的推荐方式。它会在同一进程中将服务端作为异步任务运行，从而避免子进程协调、sleep 和清理问题。
参数：
server: FastMCP 服务端实例
port: 要绑定的端口（默认: 查找可用端口）
transport: 传输类型（“http”、“streamable-http” 或 “sse”）
path: 服务端的 URL 路径（默认: “/mcp”）
host: 要绑定的 host（默认: “127.0.0.1”）

类

HeadlessOAuth 
用于测试的 OAuth provider，会绕过浏览器交互。
它通过发起 HTTP 请求以编程方式模拟完整 OAuth 流程，而不是打开浏览器并运行回调服务端。适用于自动化测试。
方法：

redirect_handler 
redirect_handler(self, authorization_url: str) -> None

向授权 URL 发起 HTTP 请求，并存储响应供回调处理器使用。

callback_handler 
callback_handler(self) -> tuple[str, str | None]

解析存储的响应并返回 (auth_code, state)。
tasks
timeout

fastmcp.utilities.timeout
函数
normalize_timeout_to_timedelta 
normalize_timeout_to_seconds 
fastmcp.utilities
timeout

fastmcp.utilities.timeout
超时值归一化实用工具。

函数

normalize_timeout_to_timedelta 
normalize_timeout_to_timedelta(value: int | float | datetime.timedelta | None) -> datetime.timedelta | None

将超时值归一化为 timedelta。
参数：
value: 超时值，可以是 int/float（秒）、timedelta 或 None
返回：
如果提供了值，则返回 timedelta；否则返回 None

normalize_timeout_to_seconds 
normalize_timeout_to_seconds(value: int | float | datetime.timedelta | None) -> float | None

将超时值归一化为秒（float）。
参数：
value: 超时值，可以是 int/float（秒）、timedelta 或 None。零值会被视为“已禁用”并返回 None。
返回：
如果提供了非零值，则返回 float 秒数；否则返回 None
tests
token_cache

fastmcp.utilities.token_cache
类
TokenCache 
enabled 
get 
set 
fastmcp.utilities
token_cache

fastmcp.utilities.token_cache
token 验证结果的内存缓存。
为 AccessToken 对象提供通用的基于 TTL 的缓存，旨在减少 opaque-token 验证期间的重复网络调用。只应缓存成功的验证结果；错误和失败必须在每次请求时重试。
示例：
from fastmcp.utilities.token_cache import TokenCache

cache = TokenCache(ttl_seconds=300, max_size=10000)

# 缓存未命中时，调用上游验证器并存储结果。
hit, token = cache.get(raw_token)
if not hit:
    token = await _call_upstream(raw_token)
    if token is not None:
        cache.set(raw_token, token)

类

TokenCache 
AccessToken 对象的基于 TTL 的内存缓存。
特性：
使用 SHA-256 哈希的缓存键（固定大小，与 token 长度无关）。
每个条目的 TTL 同时考虑配置的 ttl_seconds 和 token 自身的 expires_at claim（取更早者）。
有界大小，缓存满时使用 FIFO 淘汰。
定期清理过期条目，防止无界增长。
存储和读取时都进行防御性深拷贝，防止调用方修改缓存值。
当 ttl_seconds 为 None 或 0，或 max_size 为 0 时，缓存会被禁用。负值会抛出 ValueError。
方法：

enabled 
enabled(self) -> bool

返回缓存是否处于启用状态。

get 
get(self, token: str) -> tuple[bool, AccessToken | None]

查找缓存的验证结果。
返回：
缓存命中时返回 (True, AccessToken)；未命中或缓存禁用时返回 (False, None)。返回的 AccessToken 是深拷贝，可以安全修改。

set 
set(self, token: str, result: AccessToken) -> None

存储一次成功的验证结果。
只应缓存成功的验证。失败情况（非活跃 token、缺失 scopes、HTTP 错误、超时）不得缓存，以避免瞬时问题产生粘性的假阴性结果。
timeout
types

