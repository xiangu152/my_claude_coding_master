# FastMCP 知识库

> **版本**: latest | **来源**: fastmcp.wiki | **文件数**: 115 | **总大小**: ~1.2MB

## 文档层级结构

```
~/.claude/knowledge/agent/mcp/fastmcp/
├── summary_index.md              ← 本文件（分层抽象）
└── details/                      ← 完整原始文档（115 文件）
    ├── 01~06: Getting Started    ← 入门 + 升级指南
    ├── 07~28: Server Core        ← 服务端核心功能
    ├── 29~35: Server Auth        ← 认证与授权
    ├── 36~41: Providers          ← 组件提供方
    ├── 42~48: Transforms         ← 组件转换
    ├── 49~65: Clients            ← 客户端功能
    ├── 66~78: Apps               ← 应用 UI 框架
    ├── 79~83: Deployment         ← 部署方案
    ├── 84~90: CLI                ← 命令行工具
    ├── 91~94: Patterns & More    ← 模式、FAQ、设置
    ├── 95~109: Python SDK        ← SDK API 参考（15 文件）
    └── 110~115: Integrations     ← 第三方集成（6 文件）
```

## 架构总览

```
FastMCP 框架
├── Server（服务端）
│   ├── FastMCP 类              → 核心服务器，注册 Tool/Resource/Prompt
│   ├── Tool                    → @mcp.tool() 暴露可执行函数
│   ├── Resource                → @mcp.resource() 暴露数据源
│   ├── Prompt                  → @mcp.prompt() 暴露提示模板
│   ├── Context                 → MCP 上下文（日志/进度/采样/征询）
│   ├── Lifespan                → 服务端生命周期管理
│   ├── Middleware               → 请求/响应拦截器
│   ├── Composition             → mount() / import_server() 组合
│   ├── Dependency Injection    → Depends() 注入运行时值
│   └── Auth                    → OAuth 2.1 / Bearer / API Key 认证
│
├── Client（客户端）
│   ├── Client 类               → 连接 MCP 服务端的编程客户端
│   ├── Transports              → stdio / SSE / StreamableHTTP
│   ├── call_tool()             → 调用远程工具
│   ├── read_resource()         → 读取远程资源
│   ├── get_prompt()            → 获取远程提示词
│   └── Auth                    → Bearer / OAuth / CIMD 认证
│
├── Apps（应用 UI）
│   ├── FastMCPApp              → 将工具绑定到交互式 UI
│   ├── Prefab                  → 图表/表格/仪表盘预制组件
│   ├── Generative UI           → LLM 动态构建 UI
│   ├── Custom HTML             → 自定义 HTML/CSS/JS
│   └── Providers               → 审批/选择/文件上传/表单
│
├── Providers（组件来源）
│   ├── Local                   → 装饰器注册（默认）
│   ├── Filesystem              → 从 Python 文件自动发现
│   ├── Custom                  → 自定义数据源
│   ├── Proxy                   → 代理其他 MCP 服务端
│   └── Skills                  → Agent skills 暴露
│
├── Transforms（组件转换）
│   ├── Namespace               → 前缀命名空间
│   ├── Resources/Prompts as Tools → 向只支持工具的客户端暴露
│   ├── Tool Transformation     → 修改工具 schema
│   ├── Tool Search             → 按需搜索替代全量加载
│   └── Code Mode               → LLM 编写 Python 编排工具
│
├── Deployment（部署）
│   ├── HTTP                    → uvicorn/Starlette HTTP 部署
│   ├── Prefect Horizon         → 托管 MCP 平台
│   ├── Sandboxed Agents        → 隔离 Agent 工具暴露
│   └── Server Configuration    → fastmcp.json 声明式配置
│
├── CLI（命令行）
│   ├── fastmcp run             → 运行服务端
│   ├── fastmcp inspect         → 检查服务端组件
│   ├── fastmcp install         → 安装到 Claude/Cursor/Gemini
│   ├── fastmcp client          → 列出/调用工具
│   └── fastmcp generate-cli    → 从 MCP 服务端生成 CLI
│
└── Integrations（集成）
    ├── AI 客户端               → Claude Desktop/Code, Cursor, ChatGPT, Gemini, Goose
    ├── AI APIs                 → OpenAI, Anthropic
    ├── 框架                    → FastAPI, OpenAPI, Pydantic AI
    └── OAuth 提供商            → Auth0, AuthKit, AWS, Azure, GitHub, Google, etc.
```

## 文件索引

| # | 文件名 | 主题 | 大小 |
|---|--------|------|------|
| **Getting Started** | | | |
| 01 | 01-welcome.md | 欢迎：FastMCP 概述 | 2KB |
| 02 | 02-installation.md | 安装：pip/uv 安装与验证 | 2KB |
| 03 | 03-quickstart.md | 快速开始：首个 MCP 服务端 | 3KB |
| 04 | 04-upgrade-v2.md | 从 FastMCP 2 升级 | 13KB |
| 05 | 05-upgrade-sdk.md | 从 MCP Low-Level SDK 升级 | 14KB |
| 06 | 06-upgrade-mcp.md | 从 MCP SDK 升级 | 3KB |
| **Server Core** | | | |
| 07 | 07-server.md | FastMCP 服务端类 | 6KB |
| 08 | 08-tools.md | 工具：@mcp.tool() | 16KB |
| 09 | 09-resources.md | 资源和模板：@mcp.resource() | 15KB |
| 10 | 10-prompts.md | 提示词：@mcp.prompt() | 7KB |
| 11 | 11-context.md | MCP 上下文 | 10KB |
| 12 | 12-lifespan.md | 生命周期管理 | 3KB |
| 13 | 13-logging.md | 客户端日志 | 2KB |
| 14 | 14-progress.md | 进度报告 | 1KB |
| 15 | 15-sampling.md | LLM 采样 | 5KB |
| 16 | 16-elicitation.md | 用户征询 | 5KB |
| 17 | 17-middleware.md | MCP 中间件 | 19KB |
| 18 | 18-composition.md | 服务器组合 | 8KB |
| 19 | 19-dependency-injection.md | 依赖注入 | 12KB |
| 20 | 20-authorization.md | 授权 | 13KB |
| 21 | 21-visibility.md | 组件可见性 | 14KB |
| 22 | 22-pagination.md | 分页 | 2KB |
| 23 | 23-versioning.md | 版本管理 | 13KB |
| 24 | 24-icons.md | 图标 | 3KB |
| 25 | 25-storage.md | 存储后端 | 6KB |
| 26 | 26-tasks.md | 后台任务 | 5KB |
| 27 | 27-testing.md | 测试服务端 | 2KB |
| 28 | 28-telemetry.md | OpenTelemetry | 12KB |
| **Server Auth** | | | |
| 29 | 29-authentication.md | 身份验证概述 | 8KB |
| 30 | 30-remote-oauth.md | 远程 OAuth | 7KB |
| 31 | 31-token-verification.md | 令牌验证 | 7KB |
| 32 | 32-full-oauth-server.md | 完整 OAuth 服务器 | 2KB |
| 33 | 33-oauth-proxy.md | OAuth 代理 | 13KB |
| 34 | 34-oidc-proxy.md | OIDC 代理 | 5KB |
| 35 | 35-multi-auth.md | 多个认证来源 | 3KB |
| **Providers** | | | |
| 36 | 36-providers.md | Providers 概览 | 2KB |
| 37 | 37-provider-local.md | Local Provider | 3KB |
| 38 | 38-provider-filesystem.md | 文件系统提供方 | 5KB |
| 39 | 39-provider-custom.md | 自定义提供方 | 5KB |
| 40 | 40-provider-proxy.md | MCP Proxy Provider | 9KB |
| 41 | 41-provider-skills.md | Skills 提供方 | 11KB |
| **Transforms** | | | |
| 42 | 42-transforms.md | 转换概览 | 4KB |
| 43 | 43-namespace.md | 命名空间转换 | 1KB |
| 44 | 44-resources-as-tools.md | 将资源作为工具 | 3KB |
| 45 | 45-prompts-as-tools.md | 将提示词作为工具 | 3KB |
| 46 | 46-tool-transformation.md | 工具转换 | 7KB |
| 47 | 47-tool-search.md | 工具搜索 | 4KB |
| 48 | 48-code-mode.md | 代码模式 | 15KB |
| **Clients** | | | |
| 49 | 49-client.md | FastMCP 客户端 | 5KB |
| 50 | 50-client-only.md | 仅客户端包 | 2KB |
| 51 | 51-transports.md | 客户端传输 | 5KB |
| 52 | 52-remote.md | fastmcp-remote | 3KB |
| 53 | 53-client-tools.md | 调用工具 | 4KB |
| 54 | 54-client-resources.md | 读取资源 | 2KB |
| 55 | 55-client-prompts.md | 获取提示词 | 3KB |
| 56 | 56-client-roots.md | 客户端根目录 | 1KB |
| 57 | 57-client-sampling.md | LLM 采样 | 3KB |
| 58 | 58-client-elicitation.md | 用户征询 | 3KB |
| 59 | 59-client-logging.md | 服务端日志 | 2KB |
| 60 | 60-client-notifications.md | 通知 | 4KB |
| 61 | 61-client-progress.md | 进度监控 | 1KB |
| 62 | 62-client-tasks.md | 后台任务 | 3KB |
| 63 | 63-client-bearer.md | Bearer Token 认证 | 2KB |
| 64 | 64-client-oauth.md | OAuth 认证 | 5KB |
| 65 | 65-client-cimd.md | CIMD 认证 | 3KB |
| **Apps** | | | |
| 66 | 66-apps-overview.md | 应用概览 | 1KB |
| 67 | 67-apps-quickstart.md | 应用快速开始 | 6KB |
| 68 | 68-fastmcp-app.md | FastMCPApp | 11KB |
| 69 | 69-prefab.md | 交互式工具 (Prefab) | 8KB |
| 70 | 70-generative.md | 生成式 UI | 3KB |
| 71 | 71-low-level.md | 自定义 HTML 应用 | 7KB |
| 72 | 72-architecture.md | 应用架构 | 5KB |
| 73 | 73-development.md | 应用开发 | 1KB |
| 74 | 74-apps-examples.md | 应用示例 | 1KB |
| 75 | 75-approval.md | 审批 | 2KB |
| 76 | 76-choice.md | 选择 | 1KB |
| 77 | 77-file-upload.md | 文件上传 | 3KB |
| 78 | 78-form.md | 表单输入 | 2KB |
| **Deployment** | | | |
| 79 | 79-deploy-http.md | HTTP 部署 | 13KB |
| 80 | 80-deploy-running.md | 运行服务器 | 6KB |
| 81 | 81-deploy-config.md | 项目配置 (fastmcp.json) | 7KB |
| 82 | 82-deploy-horizon.md | Prefect Horizon | 3KB |
| 83 | 83-deploy-sandbox.md | 沙箱化 Agent | 5KB |
| **CLI** | | | |
| 84 | 84-cli-overview.md | CLI 概览 | 2KB |
| 85 | 85-cli-running.md | 运行服务端 | 4KB |
| 86 | 86-cli-inspecting.md | 检查服务端 | 1KB |
| 87 | 87-cli-install.md | 安装 MCP 服务端 | 3KB |
| 88 | 88-cli-client.md | 客户端命令 | 3KB |
| 89 | 89-cli-auth.md | 认证工具 | 2KB |
| 90 | 90-cli-generate.md | 生成 CLI | 2KB |
| **Patterns & More** | | | |
| 91 | 91-contrib.md | Contrib 模块 | 1KB |
| 92 | 92-faq.md | 常见问题 | 1KB |
| 93 | 93-settings.md | 设置 | 4KB |
| 94 | 94-updates.md | 更新时间线 | 6KB |
| **Python SDK Reference** | | | |
| 95 | 95-sdk-server.md | SDK: Server 类 | 1KB |
| 96 | 96-sdk-tools.md | SDK: Tools | 1KB |
| 97 | 97-sdk-resources.md | SDK: Resources | 1KB |
| 98 | 98-sdk-prompts.md | SDK: Prompts | 1KB |
| 99 | 99-sdk-client.md | SDK: Client | 1KB |
| 100 | 100-sdk-decorators.md | SDK: Decorators & Dependencies | 1KB |
| 101 | 101-sdk-settings-types.md | SDK: Settings, Types, Exceptions | 2KB |
| 102 | 102-sdk-cli-telemetry.md | SDK: CLI & Telemetry | 4KB |
| 103 | 103-sdk-utilities.md | SDK: Utilities (Logging, Types, HTTP, Auth) | 8KB |
| 104 | 104-sdk-utilities-cont.md | SDK: Utilities (Inspect, Components, Parsing) | 5KB |
| 105 | 105-sdk-utilities-ext.md | SDK: Utilities (OpenAPI, Pagination, Skills) | 6KB |
| 106 | 106-sdk-utilities-ui.md | SDK: Utilities (UI, Version, Config) | 7KB |
| 107 | 107-sdk-apps.md | SDK: Apps | 6KB |
| 108 | 108-sdk-experimental.md | SDK: Experimental | 4KB |
| 109 | 109-sdk-config-env.md | SDK: Config Environments | 3KB |
| **Integrations** | | | |
| 110 | 110-int-ai-clients.md | 集成: AI 客户端 (Claude, ChatGPT, Gemini, Goose) | 27KB |
| 111 | 111-int-ai-apis.md | 集成: AI APIs (OpenAI, Anthropic) | 9KB |
| 112 | 112-int-frameworks.md | 集成: 框架 (FastAPI, OpenAPI, Pydantic AI) | 24KB |
| 113 | 113-int-config.md | 集成: MCP JSON 配置 | 7KB |
| 114 | 114-int-oauth-providers.md | 集成: OAuth 提供商 (Auth0~Google) | 47KB |
| 115 | 115-int-oauth-more.md | 集成: 更多 OAuth (Keycloak~WorkOS) | 34KB |

## 关键概念速查

| 概念 | 定义 | 详情文件 |
|------|------|----------|
| **FastMCP** | 核心服务器类，通过装饰器注册 Tool/Resource/Prompt | 07 |
| **Tool** | 可执行函数，通过 @mcp.tool() 暴露给 LLM | 08, 53, 96 |
| **Resource** | 数据源，通过 @mcp.resource() 暴露 URI 寻址的数据 | 09, 54, 97 |
| **Prompt** | 提示模板，通过 @mcp.prompt() 创建可重用模板 | 10, 55, 98 |
| **Context** | MCP 上下文对象，访问日志/进度/采样/征询等运行时能力 | 11 |
| **Client** | 编程客户端，连接并交互 MCP 服务端 | 49, 53~65 |
| **Provider** | 组件来源：Local/FileSystem/Custom/Proxy/Skills | 36~41 |
| **Transform** | 组件转换：命名空间/工具转换/代码模式 | 42~48 |
| **Middleware** | 请求/响应拦截器，添加横切功能 | 17 |
| **Composition** | mount() / import_server() 组合多个服务端 | 18 |
| **Dependency Injection** | Depends() 注入运行时值（请求/令牌/自定义） | 19 |
| **Auth** | 认证系统：OAuth 2.1 / Bearer / API Key / CIMD | 29~35, 63~65 |
| **Lifespan** | 服务端生命周期，startup/shutdown 管理 | 12 |
| **Deployment** | HTTP 部署 / Prefect Horizon / 沙箱化 Agent | 79~83 |
| **CLI** | fastmcp 命令行：run/inspect/install/client/generate | 84~90 |
| **App** | 交互式 UI 框架：FastMCPApp/Prefab/Generative/Custom | 66~78 |

## 速查表

### 创建服务端
```python
from fastmcp import FastMCP

mcp = FastMCP("My Server")

@mcp.tool()
def add(a: int, b: int) -> int:
    """Add two numbers."""
    return a + b

@mcp.resource("data://greeting")
def greeting() -> str:
    return "Hello, World!"

@mcp.prompt()
def review(code: str) -> str:
    return f"Please review this code:\n{code}"

if __name__ == "__main__":
    mcp.run()
```

### 使用客户端
```python
from fastmcp import Client

async with Client("http://localhost:8000/mcp") as client:
    tools = await client.list_tools()
    result = await client.call_tool("add", {"a": 1, "b": 2})
    resource = await client.read_resource("data://greeting")
    prompt = await client.get_prompt("review", {"code": "print(1)"})
```

### CLI 常用命令
```bash
fastmcp run server.py              # 运行服务端
fastmcp inspect server.py          # 检查服务端组件
fastmcp install server.py          # 安装到 Claude Desktop
fastmcp install server.py -c cursor # 安装到 Cursor
fastmcp client http://localhost:8000/mcp  # 连接客户端
fastmcp generate-cli http://...    # 生成独立 CLI
```

### 部署方式
```python
# HTTP 部署
mcp.run(transport="http", host="0.0.0.0", port=8000)

# 或使用 fastmcp.json
# {
#   "server": { "name": "my-server", "entrypoint": "server.py" },
#   "deployment": { "transport": "http" }
# }
```

### 认证配置
```python
from fastmcp.server.auth import BearerTokenAuth

auth = BearerTokenAuth(jwks_uri="https://auth.example.com/.well-known/jwks.json")
mcp = FastMCP("Secure Server", auth=auth)
```
