---
title: "SDK: Utilities (Logging, Types, HTTP, Auth)"
source: "https://fastmcp.wiki/zh/python-sdk/fastmcp-utilities-logging"
version: "latest"
---

# SDK: Utilities (Logging, Types, HTTP, Auth)

> 原始文档来源：https://fastmcp.wiki/zh/python-sdk/fastmcp-utilities-logging (Python SDK API Reference)

---

fastmcp.utilities.logging
函数
get_logger 
configure_logging 
temporary_log_level 
fastmcp.utilities
logging

fastmcp.utilities.logging
FastMCP 的日志实用工具。

函数

get_logger 
get_logger(name: str) -> logging.Logger

获取嵌套在 FastMCP 命名空间下的 logger。
参数：
name: logger 的名称，会加上 ‘FastMCP.’ 前缀
返回：
已配置的 logger 实例

configure_logging 
configure_logging(level: Literal['DEBUG', 'INFO', 'WARNING', 'ERROR', 'CRITICAL'] | int = 'INFO', logger: logging.Logger | None = None, enable_rich_tracebacks: bool | None = None, **rich_kwargs: Any) -> None

为 FastMCP 配置日志。
参数：
logger: 要配置的 logger
level: 要使用的日志级别
rich_kwargs: 用于创建 RichHandler 的参数

temporary_log_level 
temporary_log_level(level: str | None, logger: logging.Logger | None = None, enable_rich_tracebacks: bool | None = None, **rich_kwargs: Any)

用于临时设置日志级别并在之后恢复的上下文管理器。
参数：
level: 要设置的临时日志级别（例如 “DEBUG”、“INFO”）
logger: 要配置的可选 logger（默认为 FastMCP logger）
enable_rich_tracebacks: 是否启用 rich tracebacks
**rich_kwargs: RichHandler 的额外参数
lifespan
__init__

fastmcp.utilities.types
Functions
get_fn_name 
get_cached_typeadapter 
issubclass_safe 
is_class_member_of_type 
find_kwarg_by_type 
create_function_without_params 
replace_type 
Classes
FastMCPBaseModel 
Image 
to_image_content 
to_data_uri 
Audio 
to_audio_content 
File 
to_resource_content 
ContextSamplingFallbackProtocol 
fastmcp.utilities
types

fastmcp.utilities.types
FastMCP 中通用的类型。

Functions

get_fn_name 
get_fn_name(fn: Callable[..., Any]) -> str

get_cached_typeadapter 
get_cached_typeadapter(cls: T) -> TypeAdapter[T]

TypeAdapter 是较重的对象，在应用上下文中通常会在全局作用域创建一次，并尽可能复用。不过，对于用户生成的函数，这种方式并不可行。因此，我们使用缓存来尽量降低创建它们的成本。

issubclass_safe 
issubclass_safe(cls: type, base: type) -> bool

检查 cls 是否是 base 的子类，即使 cls 是类型变量也可以处理。

is_class_member_of_type 
is_class_member_of_type(cls: Any, base: type) -> bool

检查 cls 是否是 base 的成员，即使 cls 是类型变量也可以处理。
Base 可以是类型、UnionType 或 Annotated 类型。泛型类型不被视为成员（例如 T 不是 list[T] 的成员）。

find_kwarg_by_type 
find_kwarg_by_type(fn: Callable, kwarg_type: type) -> str | None

查找类型为 kwarg_type 的 kwarg 名称。
包含含有 kwarg_type 的 union 类型，以及 Annotated 类型。

create_function_without_params 
create_function_without_params(fn: Callable[..., Any], exclude_params: list[str]) -> Callable[..., Any]

创建一个代码相同的新函数，但从 annotations 中移除指定参数。
当某些参数无法序列化时，这用于将它们排除在类型适配器处理之外。被排除的参数会从函数的 annotations 字典中移除。

replace_type 
replace_type(type_, type_map: dict[type, type])

给定一个类型（可能是泛型、嵌套类型或其他复杂类型），将其中所有 old_type 实例替换为 new_type。
这在创建工具时转换类型很有用。
参数：
type_：要在其中将 old_type 实例替换为 new_type 的类型。
old_type：要替换的类型。
new_type：用于替换 old_type 的类型。
示例：
>>> replace_type(list[int | bool], {int: str})
list[str | bool]

>>> replace_type(list[list[int]], {int: str})
list[list[str]]

Classes

FastMCPBaseModel 
FastMCP 模型的基础模型。

Image 
用于从工具返回图片的辅助类。
Methods:

to_image_content 
to_image_content(self, mime_type: str | None = None, annotations: Annotations | None = None) -> mcp.types.ImageContent

转换为 MCP ImageContent。

to_data_uri 
to_data_uri(self, mime_type: str | None = None) -> str

以 data URI 形式获取图片。

Audio 
用于从工具返回音频的辅助类。
Methods:

to_audio_content 
to_audio_content(self, mime_type: str | None = None, annotations: Annotations | None = None) -> mcp.types.AudioContent

File 
用于从工具返回文件数据的辅助类。
Methods:

to_resource_content 
to_resource_content(self, mime_type: str | None = None, annotations: Annotations | None = None) -> mcp.types.EmbeddedResource

ContextSamplingFallbackProtocol 
token_cache
ui

fastmcp.utilities.json_schema
函数
dereference_refs 
resolve_root_ref 
compress_schema 
fastmcp.utilities
json_schema

fastmcp.utilities.json_schema

函数

dereference_refs 
dereference_refs(schema: dict[str, Any]) -> dict[str, Any]

通过内联定义解析 JSON schema 中的所有 $ref 引用。
此函数会解析指向 
𝑑
𝑒
𝑓
𝑠
的
defs的ref 引用，将其替换为实际定义内容，同时保留 Pydantic 放在 $ref 旁边的同级关键字（例如 description、default、examples）。
这是必要的，因为某些 MCP 客户端（例如 VS Code Copilot）不能正确处理工具输入 schema 中的 $ref。
对于无法完全解引用的自引用/循环 schema，此函数会回退为仅解析根级 
𝑟
𝑒
𝑓
，同时为嵌套引用保留
ref，同时为嵌套引用保留defs。
只会解析本地 $ref 值（以 # 开头的值）。远程 URI（http://、file:// 等）会在解析前被移除，以防在代理来自不可信服务端的 schema 时发生 SSRF / 本地文件包含攻击。
参数：
schema: 可能包含 $ref 引用的 JSON schema 字典
返回：
一个新的 schema 字典，其中 
𝑟
𝑒
𝑓
会在可能时被解析，且不再需要时会移除
ref会在可能时被解析，且不再需要时会移除defs

resolve_root_ref 
resolve_root_ref(schema: dict[str, Any]) -> dict[str, Any]

解析根级 $ref，以满足 MCP 规范要求。
MCP 规范要求 outputSchema 在根级具有 “type”: “object”。当 Pydantic 为自引用模型生成 schema 时，它会在根级使用指向 
𝑑
𝑒
𝑓
𝑠
的
defs的ref。此函数会通过内联被引用定义来解析这类引用，同时为嵌套引用保留 $defs。
参数：
schema: 根级可能包含 $ref 的 JSON schema 字典
返回：
解析了根级 $ref 的新 schema 字典；如果无需解析，则返回原始 schema

compress_schema 
compress_schema(schema: dict[str, Any], prune_params: list[str] | None = None, prune_additional_properties: bool = False, prune_titles: bool = False, dereference: bool = False) -> dict[str, Any]

压缩并优化 JSON schema 以兼容 MCP。
参数：
schema: 要压缩的 schema
prune_params: 要从 properties 中移除的参数名称列表
prune_additional_properties: 是否移除 additionalProperties: false。默认为 False，以维持 MCP 客户端兼容性，因为某些客户端（例如 Claude）需要 additionalProperties: false 来进行严格验证。
prune_titles: 是否从 schema 中移除 title 字段
dereference: 是否通过内联定义来解引用 $ref。默认为 False；解引用通常改由 middleware 在服务时处理。
inspect
json_schema_type

fastmcp.utilities.json_schema_type
不支持的正则模式
Functions
json_schema_to_type 
Classes
JSONSchema 
fastmcp.utilities
json_schema_type

fastmcp.utilities.json_schema_type
将 JSON Schema 转换为带验证的 Python 类型。
json_schema_to_type 函数会将 JSON Schema 转换为可用于 Pydantic 验证的 Python 类型。它支持：
基本类型（string、number、integer、boolean、null）
复杂类型（array、object）
格式约束（date-time、email、uri）
数值约束（minimum、maximum、multipleOf）
字符串约束（minLength、maxLength、pattern）
数组约束（minItems、maxItems、uniqueItems）
带默认值的对象属性
引用和递归 schema
枚举和常量
Union 类型

不支持的正则模式
Pydantic 使用基于 Rust 的正则引擎，并不支持真实 JSON Schema 中出现的所有正则特性（尤其是来自 AWS、Azure 和其他大型 OpenAPI 提供商的 schema）。不受支持的结构包括前瞻/后顾断言（(?!...)、(?<=...)）、Unicode 属性转义（\p{Graph}、\p{Print}），以及非常大的已编译模式。
当 pattern 约束无法编译时，json_schema_to_type 会优雅降级：
该模式会从 Pydantic StringConstraints 中移除，因此该类型不会引发 SchemaError。
会发出包含不支持模式的 UserWarning。
原始模式会以 x-unsupported-pattern 保存在类型元数据中（可通过 TypeAdapter(T).json_schema() 查看）。
其他约束（minLength、maxLength）仍会执行。
Example:
schema = {
    "type": "object",
    "properties": {
        "name": {"type": "string", "minLength": 1},
        "age": {"type": "integer", "minimum": 0},
        "email": {"type": "string", "format": "email"}
    },
    "required": ["name", "age"]
}

# name 是可选的；如果未提供，会从 schema 的 "title" 属性推断
Person = json_schema_to_type(schema)
# 创建一个带 name、age 和可选 email 字段的已验证 dataclass

Functions

json_schema_to_type 
json_schema_to_type(schema: Mapping[str, Any] | bool, name: str | None = None) -> type

将 JSON schema 转换为带验证的适当 Python 类型。
参数：
schema：定义类型结构和验证规则的 JSON Schema 字典。也接受布尔 schema（True = 任意类型，False = 不可满足）。
name：对象 schema 的可选名称。仅当 schema 类型为 “object” 时允许。如果对象未提供该名称，则会从 schema 的 “title” 属性推断，或默认为 “Root”。
返回：
带 Pydantic 验证的 Python 类型（对象通常为 dataclass）
抛出：
ValueError：如果为非对象 schema 提供了名称
示例：
从对象 schema 创建 dataclass：
schema = {
    "type": "object",
    "title": "Person",
    "properties": {
        "name": {"type": "string", "minLength": 1},
        "age": {"type": "integer", "minimum": 0},
        "email": {"type": "string", "format": "email"}
    },
    "required": ["name", "age"]
}

Person = json_schema_to_type(schema)
创建一个带 name、age 和可选 email 字段的 dataclass：
# @dataclass
# class Person:
#     name: str
#     age: int
#     email: str | None = None

Person(name=“John”, age=30)
创建带约束的标量类型：
schema = {
    "type": "string",
    "minLength": 3,
    "pattern": "^[A-Z][a-z]+$"
}

NameType = json_schema_to_type(schema)
# Creates Annotated[str, StringConstraints(min_length=3, pattern="^[A-Z][a-z]+$")]

@dataclass
class Name:
    name: NameType

Classes

JSONSchema 
json_schema
lifespan

fastmcp.utilities.http
函数
find_available_port 
fastmcp.utilities
http

fastmcp.utilities.http

函数

find_available_port 
find_available_port() -> int

通过让操作系统分配端口来查找一个可用端口。
exceptions
inspect

fastmcp.utilities.auth
函数
decode_jwt_header 
decode_jwt_payload 
parse_scopes 
fastmcp.utilities
auth

fastmcp.utilities.auth
身份认证实用辅助函数。

函数

decode_jwt_header 
decode_jwt_header(token: str) -> dict[str, Any]

在不验证签名的情况下解码 JWT header。
这对于提取用于 JWKS 查找的 key ID（kid）很有用。
参数：
token: JWT token 字符串（header.payload.signature）
返回：
解码后的 header 字典
抛出：
ValueError: 如果 token 不是有效的 JWT 格式

decode_jwt_payload 
decode_jwt_payload(token: str) -> dict[str, Any]

在不验证签名的情况下解码 JWT payload。
仅用于直接从可信来源（例如 IdP token 端点）收到的 token。
参数：
token: JWT token 字符串（header.payload.signature）
返回：
解码后的 payload 字典
抛出：
ValueError: 如果 token 不是有效的 JWT 格式

parse_scopes 
parse_scopes(value: Any) -> list[str] | None

从环境变量或设置值中解析 scopes。
接受 JSON 数组字符串、逗号或空格分隔的字符串、字符串列表或 None。返回 scopes 列表；如果未提供值，则返回 None。
async_utils
authorization

fastmcp.utilities.authorization
函数
require_scopes 
restrict_tag 
run_auth_checks 
类
AuthContext 
tool 
fastmcp.utilities
authorization

fastmcp.utilities.authorization
FastMCP 组件的授权检查。
授权检查是接收 AuthContext 的可调用对象，返回 True 表示允许访问，返回 False 表示拒绝访问。它们也可以抛出 AuthorizationError 并附带自定义消息来拒绝访问；其他异常会被屏蔽并视为拒绝。

函数

require_scopes 
require_scopes(*scopes: str) -> AuthCheck

要求具备给定的所有 OAuth scopes。

restrict_tag 
restrict_tag(tag: str) -> AuthCheck

当被访问组件具有特定标签时要求 scopes。

run_auth_checks 
run_auth_checks(checks: AuthCheck | list[AuthCheck], ctx: AuthContext) -> bool

使用 AND 逻辑运行授权检查。

类

AuthContext 
传递给授权检查可调用对象的上下文。
属性：
token: 当前访问 token；如果未认证，则为 None。
component: 正在访问的工具、资源、资源模板或提示词。
tool: 当 component 是 Tool 时，对 component 的向后兼容别名。
方法：

tool 
tool(self) -> Tool | None

以 Tool 形式访问 component 的向后兼容方式。
auth
cli

