---
title: "SDK: Apps (App, Approval, Choice, Form, Generative)"
source: "https://fastmcp.wiki/zh/python-sdk/fastmcp-apps-__init__"
version: "latest"
---

# SDK: Apps (App, Approval, Choice, Form, Generative)

> 原始文档来源：https://fastmcp.wiki/zh/python-sdk/fastmcp-apps-__init__ (Python SDK API Reference)

---

fastmcp.apps
fastmcp.apps
__init__

fastmcp.apps
FastMCP Apps：面向 MCP 工具的交互式 UI。
此包包含与 app 相关的组件：
FastMCPApp：带后端工具的交互式 app 的可组合 provider
AppConfig：MCP App 工具和资源的配置
ResourceCSP / ResourcePermissions：安全配置
types
app

fastmcp.apps.app
Classes
FastMCPApp 
tool 
tool 
tool 
ui 
ui 
ui 
add_tool 
lifespan 
run 
fastmcp.apps
app

fastmcp.apps.app
FastMCPApp 是表示可组合 MCP 应用的 Provider。
FastMCPApp 将入口点工具（模型调用这些工具）与后端工具（UI 通过 CallTool 调用这些工具）绑定在一起。后端工具会带上 meta["fastmcp"]["app"] 标记，因此即使转换（命名空间、可见性等）重命名或隐藏了它们，也仍能通过 provider 链找到。服务端会设置一个上下文变量，告诉 Provider.get_tool 对 app 可见工具回退到直接查找。
Usage::
from fastmcp import FastMCP, FastMCPApp
app = FastMCPApp(“Dashboard”)
@app.ui() def show_dashboard() -> Component: return Column(…)
@app.tool() def save_contact(name: str, email: str) -> str: return name
server = FastMCP(“Platform”) server.add_provider(app)

Classes

FastMCPApp 
表示 MCP 应用的 Provider。
将入口点工具（@app.ui）、后端工具（@app.tool）和 Prefab 渲染器资源绑定在一起。后端工具会带上 meta["fastmcp"]["app"] 标记，因此即使已应用转换，Provider.get_tool 也能通过原始名称找到它们。
Methods:

tool 
tool(self, name_or_fn: F) -> F

tool 
tool(self, name_or_fn: str | None = None) -> Callable[[F], F]

tool 
tool(self, name_or_fn: str | AnyFunction | None = None) -> Any

注册 UI 通过 CallTool 调用的后端工具。
后端工具默认使用 visibility=["app"]。传入 model=True 也会将该工具暴露给模型（visibility=["app", "model"]）。
支持多种调用模式::
@app.tool def save(name: str): …
@app.tool() def save(name: str): …
@app.tool(“custom_name”) def save(name: str): …

ui 
ui(self, name_or_fn: F) -> F

ui 
ui(self, name_or_fn: str | None = None) -> Callable[[F], F]

ui 
ui(self, name_or_fn: str | AnyFunction | None = None) -> Any

注册模型调用的 UI 入口点工具。
入口点工具默认使用 visibility=["model"]，并会自动连接 Prefab 渲染器资源和 CSP。它们会带上应用名称标记，因此结构化内容会包含 _meta.fastmcp.app。
支持多种调用模式::
@app.ui def dashboard() -> Component: …
@app.ui() def dashboard() -> Component: …
@app.ui(“my_dashboard”) def dashboard() -> Component: …

add_tool 
add_tool(self, tool: Tool | Callable[..., Any]) -> Tool

以编程方式向此应用添加工具。
该工具会带上此应用名称标记以便路由。

lifespan 
lifespan(self) -> AsyncIterator[None]

run 
run(self, transport: Literal['stdio', 'http', 'sse', 'streamable-http'] | None = None, **kwargs: Any) -> None

创建临时 FastMCP 服务端，并独立运行此应用。
__init__
approval

fastmcp.apps.approval
类
Approval 
fastmcp.apps
approval

fastmcp.apps.approval
Approval：一个为任意服务端添加人在回路审批的 Provider。
LLM 会展示它即将执行内容的摘要，用户通过按钮批准或拒绝。结果会作为消息发送回对话中，提示 LLM 进入下一轮。
需要 fastmcp[apps]（prefab-ui）。
用法::
from fastmcp import FastMCP from fastmcp.apps.approval import Approval
mcp = FastMCP(“My Server”) mcp.add_provider(Approval())

类

Approval 
一个为服务端添加人在回路审批的 Provider。
LLM 调用 request_approval 工具，并传入摘要和可选详情。用户会看到一张审批卡片，其中包含批准和拒绝按钮。点击任一按钮都会通过 SendMessage 向对话发送消息，从而触发 LLM 的下一轮。
这条消息看起来像是用户发送的，因此 LLM 会看到类似 '"Deploy v3.2 to production" is APPROVED' 的内容。
示例::
from fastmcp import FastMCP from fastmcp.apps.approval import Approval
mcp = FastMCP(“My Server”) mcp.add_provider(Approval())
自定义::
Approval( title=“Deploy Gate”, approve_text=“Ship it”, approve_variant=“default”, reject_text=“Abort”, reject_variant=“destructive”, )
app
choice

fastmcp.apps.choice
类
Choice 
fastmcp.apps
choice

fastmcp.apps.choice
Choice：一个允许用户从一组选项中选择的 Provider。
LLM 展示选项，用户点击其中一个，选择结果会作为消息流回对话中。
需要 fastmcp[apps]（prefab-ui）。
用法::
from fastmcp import FastMCP from fastmcp.apps.choice import Choice
mcp = FastMCP(“My Server”) mcp.add_provider(Choice())

类

Choice 
一个允许用户从一组选项中选择的 Provider。
LLM 调用 choose，并传入一个提示词和一组选项。用户会看到一张卡片，每个选项对应一个按钮。点击按钮会通过 SendMessage 将选择结果发送回对话，从而触发 LLM 的下一轮。
示例::
from fastmcp import FastMCP from fastmcp.apps.choice import Choice
mcp = FastMCP(“My Server”) mcp.add_provider(Choice())
approval
config

fastmcp.apps.config
函数
app_config_to_meta_dict 
类
ResourceCSP 
ResourcePermissions 
AppConfig 
PrefabAppConfig 
model_post_init 
fastmcp.apps
config

fastmcp.apps.config
MCP Apps 支持：扩展协商和类型化 UI 元数据模型。
为 MCP Apps 扩展（io.modelcontextprotocol/ui）提供常量和 Pydantic 模型，使工具和资源能够为支持交互式 app 渲染的客户端携带 UI 元数据。

函数

app_config_to_meta_dict 
app_config_to_meta_dict(app: AppConfig | dict[str, Any]) -> dict[str, Any]

将 AppConfig 或 dict 转换为 meta["ui"] 的线协议格式 dict。

类

ResourceCSP 
MCP App 资源的内容安全策略。
声明 app 允许连接哪些外部 origin，或允许从哪些外部 origin 加载资源。宿主会使用这些声明为沙箱 iframe 构建 Content-Security-Policy header。

ResourcePermissions 
MCP App 资源的 iframe 沙箱权限。
每个字段在设置后（通常设为 {}），都会请求宿主向沙箱 iframe 授予对应的 Permission Policy 功能。宿主可以选择遵守这些请求；app 应使用 JS 功能检测作为备用方案。

AppConfig 
MCP App 工具和资源的配置。
控制工具或资源如何参与 MCP Apps 扩展。对于工具，resource_uri 和 visibility 指定要渲染哪个 UI 资源以及工具出现的位置。对于资源，这些字段必须保持未设置（资源本身就是 UI）。
所有字段都使用 exclude_none 序列化，因此只有显式设置的值会出现在传输内容中。别名匹配 MCP Apps 的线协议格式（camelCase）。

PrefabAppConfig 
带合理默认值的 Prefab 工具 app 配置。
类似 app=True，但可自定义。它会自动连接 Prefab 渲染器 URI，并将渲染器的 CSP 与你指定的任何额外域名合并。渲染器资源会自动注册。
示例::
@mcp.tool(app=PrefabAppConfig()) # same as app=True
@mcp.tool(app=PrefabAppConfig( csp=ResourceCSP(frame_domains=[“https://example.com”]), ))
方法：

model_post_init 
model_post_init(self, __context: Any) -> None

choice
file_upload

fastmcp.apps.file_upload
写入 S3，返回摘要
从 S3 列出
从 S3 读取
Classes
FileUpload 
on_store 
on_list 
on_read 
fastmcp.apps
file_upload

fastmcp.apps.file_upload
FileUpload 是一个 Provider，可为任意服务端添加拖放式文件上传。
它允许用户通过交互式 UI 将文件直接上传到服务端，完全绕过 LLM 上下文窗口。随后，LLM 可以通过模型可见工具读取和处理已上传文件。
需要 fastmcp[apps]（prefab-ui）。
Usage::
from fastmcp import FastMCP from fastmcp.apps import FileUpload
mcp = FastMCP(“My Server”) mcp.add_provider(FileUpload())
如需自定义持久化，请覆盖存储方法::
class S3Upload(FileUpload): def on_store(self, files, ctx):

写入 S3，返回摘要
…
def on_list(self, ctx):

从 S3 列出
…
def on_read(self, name, ctx):

从 S3 读取
…

Classes

FileUpload 
为服务端添加文件上传能力的 Provider。
注册一个拖放式 UI 工具、一个后端存储工具，以及用于列出和读取已上传文件的模型可见工具。
默认情况下，文件按 MCP 会话限定作用域，并存储在内存中。覆盖 on_store、on_list 和 on_read 可实现自定义持久化（文件系统、S3、数据库等）。每个方法都会接收当前 Context，从而可访问会话 ID、认证 token 和请求元数据，用于分区和授权。
会话作用域： 默认存储使用 ctx.session_id 按会话隔离文件。它适用于 stdio、SSE 和有状态 HTTP 传输。在无状态 HTTP 模式下，每个请求都会创建新会话，因此文件不会跨请求保留。对于无状态部署，请覆盖存储方法，并基于认证上下文中的稳定标识符进行分区::
class UserScopedUpload(FileUpload): def on_store(self, files, ctx): user_id = ctx.access_token[“sub”] …
Example::
from fastmcp import FastMCP from fastmcp.apps.file_upload import FileUpload
mcp = FastMCP(“My Server”) mcp.add_provider(FileUpload())
Methods:

on_store 
on_store(self, files: list[dict[str, Any]], ctx: Context) -> list[dict[str, Any]]

存储已上传文件并返回摘要。
参数：
files：文件字典列表，每个字典包含 name、size、type 和 data（base64 编码内容）。
ctx：当前请求上下文。可用于会话 ID、认证 token，或分区所需的任何元数据。
覆盖此方法可实现自定义持久化。默认实现会将文件存储在内存中，并由 _get_scope_key(ctx) 限定作用域。
返回：
文件摘要字典列表（name、type、size、size_display、uploaded_at）。

on_list 
on_list(self, ctx: Context) -> list[dict[str, Any]]

列出所有已存储文件。
参数：
ctx：当前请求上下文。
覆盖此方法可实现自定义持久化。默认实现会返回当前作用域中的文件。
返回：
文件摘要字典列表。

on_read 
on_read(self, name: str, ctx: Context) -> dict[str, Any]

按名称读取文件内容。
参数：
name：要读取的文件名。
ctx：当前请求上下文。
覆盖此方法可实现自定义持久化。默认实现会从当前作用域的内存存储中读取。文本文件会从 base64 解码；二进制文件会返回截断后的 base64 预览。
返回：
包含文件元数据和 content（文本）或 content_base64（二进制预览）的字典。
抛出：
ValueError：如果未找到文件。
config
form

fastmcp.apps.form
类
FormInput 
fastmcp.apps
form

fastmcp.apps.form
FormInput：一个从用户收集结构化输入的 Provider。
为所需数据定义一个 Pydantic 模型，FormInput 会生成表单 UI。用户填写表单后，提交内容会被验证，并可由可选回调处理结果。
需要 fastmcp[apps]（prefab-ui）。
用法::
from pydantic import BaseModel from fastmcp import FastMCP from fastmcp.apps.form import FormInput
class ShippingAddress(BaseModel): street: str city: str state: str zip_code: str
mcp = FastMCP(“My Server”) mcp.add_provider(FormInput(model=ShippingAddress))

类

FormInput 
一个通过 Pydantic 模型收集结构化输入的 Provider。
为所需数据定义一个模型，FormInput 会使用 Form.from_model() 从中生成表单。字段类型、标签、描述和验证规则都会从模型派生。
可以选择提供 on_submit 回调来处理验证后的数据。回调会接收一个模型实例，并返回一个发送回 LLM 的字符串。如果没有回调，则直接发送验证后的 JSON。
示例::
from pydantic import BaseModel from fastmcp import FastMCP from fastmcp.apps.form import FormInput
class Contact(BaseModel): name: str email: str
mcp = FastMCP(“My Server”) mcp.add_provider(FormInput(model=Contact))
带回调::
def save_contact(contact: Contact) -> str: db.insert(contact.model_dump()) return f”Saved ”
mcp.add_provider(FormInput(model=Contact, on_submit=save_contact))
file_upload
generative

fastmcp.apps.generative
类
GenerativeUI 
lifespan 
fastmcp.apps
generative

fastmcp.apps.generative
GenerativeUI：一个添加 LLM 生成 UI 能力的 Provider。
注册来自 prefab_ui.generative 的工具和资源，使 LLM 能够编写 Prefab Python 代码，在沙箱中执行，并将结果渲染为流式交互 UI。
需要 fastmcp[apps]（prefab-ui）。
用法::
from fastmcp import FastMCP from fastmcp.apps.generative import GenerativeUI
mcp = FastMCP(“My Server”) mcp.add_provider(GenerativeUI())

类

GenerativeUI 
一个为服务端添加生成式 UI 能力的 Provider。
注册：
一个 generate_ui 工具，接受 Prefab Python 代码，在 Pyodide 沙箱中执行，并返回渲染后的 PrefabApp。支持通过 ontoolinputpartial 流式传输。
一个用于搜索 Prefab 组件库的 components 工具。
生成式渲染器资源，并附带用于访问 Pyodide CDN 的 CSP。
示例::
from fastmcp import FastMCP from fastmcp.apps.generative import GenerativeUI
mcp = FastMCP(“My Server”) mcp.add_provider(GenerativeUI())
方法：

lifespan 
lifespan(self) -> AsyncIterator[None]

form
__init__

