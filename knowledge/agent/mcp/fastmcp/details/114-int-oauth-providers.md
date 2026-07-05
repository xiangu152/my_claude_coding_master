---
title: "集成: OAuth 提供商 (Auth0, AuthKit, AWS, Azure, Descope, Discord, Eunomia, GitHub, Google)"
source: "https://fastmcp.wiki/zh/integrations/auth0"
version: "latest"
---

# 集成: OAuth 提供商 (Auth0, AuthKit, AWS, Azure, Descope, Discord, Eunomia, GitHub, Google)

> 原始文档来源：https://fastmcp.wiki/zh/integrations/auth0 (FastMCP 集成文档)

---

配置
前提条件
步骤 1：创建 Auth0 应用程序
步骤 2：FastMCP 配置
测试
运行服务器
使用客户端测试
生产环境配置
环境变量
提供者选择
Auth0 特定配置
认证
Auth0 OAuth 🤝 FastMCP

使用 Auth0 OAuth 保护您的 FastMCP 服务器

版本
2.12.4
新增
本指南向您展示如何使用 Auth0 OAuth 保护您的 FastMCP 服务器。虽然 Auth0 支持动态客户端注册，但默认情况下未启用，因此此集成使用 OIDC 代理 模式来桥接 Auth0 的动态 OIDC 配置与 MCP 的身份验证需求。

配置

前提条件
开始之前，您需要：
一个有权限创建应用程序的 Auth0 账户
您的 FastMCP 服务器 URL（开发环境可以是 localhost，例如 http://localhost:8000）

步骤 1：创建 Auth0 应用程序
在您的 Auth0 设置中创建一个应用程序以获取身份验证所需的凭据：
1

导航到应用程序

在您的 Auth0 账户中转到 Applications → Applications。
点击 ”+ Create Application” 创建新应用程序。
2

创建您的应用程序

名称: 选择用户能识别的名称（例如，“My FastMCP Server”）
选择应用程序类型: 选择 “Single Page Web Applications”
点击 Create 创建应用程序
3

配置您的应用程序

选择您应用程序的 “Settings” 标签，然后找到 “Application URIs” 部分。
Allowed Callback URLs: 您的服务器 URL + /auth/callback（例如，http://localhost:8000/auth/callback）
点击 Save 保存您的更改
回调 URL 必须完全匹配。默认路径是 /auth/callback，但您可以使用 redirect_path 参数自定义它。
如果您想使用自定义回调路径（例如，/auth/auth0/callback），请确保在 Auth0 应用程序设置和配置 Auth0Provider 时的 redirect_path 参数中设置相同的路径。
4

保存您的凭据

创建应用程序后，在 “Basic Information” 部分您将看到：
Client ID: 类似 tv2ObNgaZAWWhhycr7Bz1LU2mxlnsmsB 的公共标识符
Client Secret: 应始终安全存储的私有隐藏值
安全存储这些凭据。永远不要将它们提交到版本控制。在生产环境中使用环境变量或密钥管理器。
5

选择您的受众

在您的 Auth0 账户中转到 Applications → APIs。
找到您想为应用程序使用的 API
API Audience: 唯一标识 API 的 URL
将此与上述凭据一起存储。永远不要将其提交到版本控制。在生产环境中使用环境变量或密钥管理器。

步骤 2：FastMCP 配置
使用 Auth0Provider 创建您的 FastMCP 服务器。
server.py
from fastmcp import FastMCP
from fastmcp.server.auth.providers.auth0 import Auth0Provider

# Auth0Provider 使用 Auth0 OIDC 配置
auth_provider = Auth0Provider(
    config_url="https://.../.well-known/openid-configuration",  # 您的 Auth0 配置 URL
    client_id="tv2ObNgaZAWWhhycr7Bz1LU2mxlnsmsB",               # 您的 Auth0 应用程序客户端 ID
    client_secret="vPYqbjemq...",                               # 您的 Auth0 应用程序客户端密钥
    audience="https://...",                                     # 您的 Auth0 API 受众
    base_url="http://localhost:8000",                           # 必须与您的应用程序配置匹配
    # redirect_path="/auth/callback"                            # 默认值，如需要可自定义
)

mcp = FastMCP(name="Auth0 Secured App", auth=auth_provider)

# 添加一个受保护的工具来测试身份验证
@mcp.tool
async def get_token_info() -> dict:
    """返回关于 Auth0 令牌的信息。"""
    from fastmcp.server.dependencies import get_access_token

    token = get_access_token()

    return {
        "issuer": token.claims.get("iss"),
        "audience": token.claims.get("aud"),
        "scope": token.claims.get("scope")
    }

测试

运行服务器
使用 HTTP 传输启动您的 FastMCP 服务器以启用 OAuth 流程：
fastmcp run server.py --transport http --port 8000

您的服务器现在正在运行并受到 Auth0 身份验证保护。

使用客户端测试
创建一个与您的 Auth0 保护服务器进行身份验证的测试客户端：
test_client.py
from fastmcp import Client
import asyncio

async def main():
    # 客户端将自动处理 Auth0 OAuth 流程
    async with Client("http://localhost:8000/mcp/", auth="oauth") as client:
        # 首次连接将在您的浏览器中打开 Auth0 登录页面
        print("✓ 已通过 Auth0 认证！")

        # 测试受保护的工具
        result = await client.call_tool("get_token_info")
        print(f"Auth0 受众: {result['audience']}")

if __name__ == "__main__":
    asyncio.run(main())

当您首次运行客户端时：
您的浏览器将打开 Auth0 的授权页面
授权应用程序后，您将被重定向回来
客户端接收令牌并可以进行身份验证请求

生产环境配置
版本
2.13.0
新增
在生产部署中，为了在服务器重启后仍能保留令牌，需同时配置 jwt_signing_key 与 client_storage：
server.py
import os
from fastmcp import FastMCP
from fastmcp.server.auth.providers.auth0 import Auth0Provider
from key_value.aio.stores.redis import RedisStore
from key_value.aio.wrappers.encryption import FernetEncryptionWrapper
from cryptography.fernet import Fernet

# 使用加密的持久化令牌存储进行生产配置
auth_provider = Auth0Provider(
    config_url="https://.../.well-known/openid-configuration",
    client_id="tv2ObNgaZAWWhhycr7Bz1LU2mxlnsmsB",
    client_secret="vPYqbjemq...",
    audience="https://...",
    base_url="https://your-production-domain.com",

    # 生产环境令牌管理
    jwt_signing_key=os.environ["JWT_SIGNING_KEY"],
    client_storage=FernetEncryptionWrapper(
        key_value=RedisStore(
            host=os.environ["REDIS_HOST"],
            port=int(os.environ["REDIS_PORT"])
        ),
        fernet=Fernet(os.environ["STORAGE_ENCRYPTION_KEY"])
    )
)

mcp = FastMCP(name="Production Auth0 App", auth=auth_provider)

jwt_signing_key 与 client_storage 需要配套使用，以确保令牌与客户端注册在服务器重启后仍然有效。请使用 FernetEncryptionWrapper 对存储中的敏感 OAuth 令牌进行加密，否则将以明文形式保存。建议使用环境变量存放密钥，并选择 Redis 等持久化后端以支持分布式部署。
更多参数说明请参阅 OAuth 代理文档。
客户端在本地缓存令牌，因此除非令牌过期或您明确清除缓存，否则后续运行时无需重新认证。

环境变量
对于生产部署，使用环境变量而不是硬编码凭据。

提供者选择
设置此环境变量允许自动使用 Auth0 提供者，而无需在代码中显式实例化它。

FASTMCP_SERVER_AUTH
默认值:"Not set"
设置为 fastmcp.server.auth.providers.auth0.Auth0Provider 以使用 Auth0 身份验证。

Auth0 特定配置
这些环境变量为 Auth0 提供者提供默认值，无论是手动实例化还是通过 FASTMCP_SERVER_AUTH 配置。

FASTMCP_SERVER_AUTH_AUTH0_CONFIG_URL
必填
您的 Auth0 应用程序配置 URL（例如，https://.../.well-known/openid-configuration）

FASTMCP_SERVER_AUTH_AUTH0_CLIENT_ID
必填
您的 Auth0 应用程序客户端 ID（例如，tv2ObNgaZAWWhhycr7Bz1LU2mxlnsmsB）

FASTMCP_SERVER_AUTH_AUTH0_CLIENT_SECRET
必填
您的 Auth0 应用程序客户端密钥（例如，vPYqbjemq...）

FASTMCP_SERVER_AUTH_AUTH0_AUDIENCE
必填
您的 Auth0 API 受众

FASTMCP_SERVER_AUTH_AUTH0_BASE_URL
必填
OAuth 端点可访问的公共 URL（包含任何挂载路径）

FASTMCP_SERVER_AUTH_AUTH0_ISSUER_URL
默认值:"使用 BASE_URL"
用于 OAuth 元数据的 Issuer URL（默认等于 BASE_URL）。当挂载在路径前缀下时，请设置为根级 URL 以避免 404 日志。详见 HTTP 部署指南。

FASTMCP_SERVER_AUTH_AUTH0_REDIRECT_PATH
默认值:"/auth/callback"
在您的 Auth0 应用程序中配置的重定向路径

FASTMCP_SERVER_AUTH_AUTH0_REQUIRED_SCOPES
默认值:"[\"openid\"]"
逗号、空格或 JSON 分隔的所需 Auth0 作用域列表（例如，openid email 或 ["openid","email"]）
Example .env file:
# 使用 Auth0 提供者
FASTMCP_SERVER_AUTH=fastmcp.server.auth.providers.auth0.Auth0Provider

# Auth0 配置和凭据
FASTMCP_SERVER_AUTH_AUTH0_CONFIG_URL=https://.../.well-known/openid-configuration
FASTMCP_SERVER_AUTH_AUTH0_CLIENT_ID=tv2ObNgaZAWWhhycr7Bz1LU2mxlnsmsB
FASTMCP_SERVER_AUTH_AUTH0_CLIENT_SECRET=vPYqbjemq...
FASTMCP_SERVER_AUTH_AUTH0_AUDIENCE=https://...
FASTMCP_SERVER_AUTH_AUTH0_BASE_URL=https://your-server.com
FASTMCP_SERVER_AUTH_AUTH0_REQUIRED_SCOPES=openid,email

With environment variables set, your server code simplifies to:
server.py
from fastmcp import FastMCP

# 身份验证自动从环境中配置
mcp = FastMCP(name="Auth0 Secured App")

@mcp.tool
async def search_logs() -> list[str]:
    """搜索服务日志。"""
    # 您的工具实现在这里
    pass

Bearer Token 认证

AuthKit 🤝 FastMCP

x

---

配置
前置条件
第 1 步：WorkOS Dashboard
第 2 步：FastMCP 配置
测试
生产配置
认证
AuthKit 🤝 FastMCP

使用 WorkOS 的 AuthKit 保护你的 FastMCP 服务端

版本
2.11.0
新增
本指南展示如何使用 WorkOS 的 AuthKit 保护 FastMCP 服务端。AuthKit 是完整的身份认证和用户管理解决方案。此集成使用带 RFC 8707 resource indicators 的 Remote OAuth 模式：AuthKit 会签发 aud claim 绑定到服务端资源 URL 的 token，FastMCP 会自动验证该 claim。

配置

前置条件
开始前，你需要：
一个 WorkOS Account 和一个新的 Project。
在 WorkOS project 中配置好的 AuthKit 实例。
FastMCP 服务端的 URL（开发时可以是 localhost，例如 http://127.0.0.1:8000）。

第 1 步：WorkOS Dashboard
在 WorkOS Dashboard 中，进入 Connect → Configuration 并配置：
1

MCP Auth

启用 Dynamic Client Registration（DCR），让 MCP 客户端可以自行注册。或者，如果你的客户端支持，也可以启用 Client ID Metadata Document（CIMD）。
2

MCP resource indicators

将 FastMCP 服务端的资源 URL（例如 http://127.0.0.1:8000/mcp）添加为有效 resource indicator。
它必须与 FastMCP 在其 protected resource metadata 中公布的值完全匹配。先启动服务端，它会在启动时记录正确 URL，请复制该值。
如果没有此步骤，AuthKit 会回退到默认的环境级 audience，audience 验证会以 401 失败。
3

记录你的 AuthKit Domain

在配置页面找到你的 AuthKit Domain。它看起来像 https://your-project-12345.authkit.app。FastMCP 服务端配置会需要它。

第 2 步：FastMCP 配置
创建 FastMCP 服务端文件，并使用 AuthKitProvider 自动处理所有 OAuth 集成：
server.py
from fastmcp import FastMCP
from fastmcp.server.auth.providers.workos import AuthKitProvider

# AuthKitProvider 会自动发现 WorkOS 端点、配置 JWT 验证，
# 并将 token audience 绑定到此服务端的资源 URL。
auth_provider = AuthKitProvider(
    authkit_domain="https://your-project-12345.authkit.app",
    base_url="http://127.0.0.1:8000",  # 使用你的实际服务端 URL
)

mcp = FastMCP(name="AuthKit Secured App", auth=auth_provider)

服务端启动时，会记录它正在验证的资源 URL。将该 URL 粘贴到 Dashboard 的 MCP resource indicators 列表中。

测试
要测试服务端，可以使用 fastmcp CLI 在本地运行它。假设你已将上述代码保存为 server.py（并将 authkit_domain 和 base_url 替换为实际值！），可以运行以下命令：
fastmcp run server.py --transport http --port 8000

AuthKit 默认让 DCR 客户端使用 client_secret_basic 进行 token exchange，这会与某些 MCP 客户端发送凭据的方式冲突。为避免 token exchange 错误，请通过将 token_endpoint_auth_method 设置为 "none" 注册为 public client：
client.py
from fastmcp import Client
from fastmcp.client.auth import OAuth
import asyncio

auth = OAuth(additional_client_metadata={"token_endpoint_auth_method": "none"})

async def main():
    async with Client("http://127.0.0.1:8000/mcp", auth=auth) as client:
        assert await client.ping()

if __name__ == "__main__":
    asyncio.run(main())

生产配置
对于生产部署，请从环境变量加载敏感配置：
server.py
import os
from fastmcp import FastMCP
from fastmcp.server.auth.providers.workos import AuthKitProvider

# 从环境变量加载配置
auth = AuthKitProvider(
    authkit_domain=os.environ.get("AUTHKIT_DOMAIN"),
    base_url=os.environ.get("BASE_URL", "https://your-server.com"),
)

mcp = FastMCP(name="AuthKit Secured App", auth=auth)

Auth0 OAuth 🤝 FastMCP

AWS Cognito OAuth 🤝 FastMCP

x

---

配置
前提条件
步骤 1：创建 AWS Cognito 用户池和应用程序客户端
步骤 2：FastMCP 配置
测试
运行服务器
使用客户端测试
生产环境配置
环境变量
提供者选择
AWS Cognito 特定配置
功能
JWT 令牌验证
用户声明和组
企业集成
认证
AWS Cognito OAuth 🤝 FastMCP

使用 AWS Cognito 用户池保护您的 FastMCP 服务器

版本
2.12.4
新增
本指南向您展示如何使用 AWS Cognito 用户池保护您的 FastMCP 服务器。由于 AWS Cognito 不支持动态客户端注册，此集成使用 OAuth 代理 模式来桥接 AWS Cognito 的传统 OAuth 与 MCP 的身份验证需求。它还包括强大的 JWT 令牌验证，确保企业级身份验证。

配置

前提条件
开始之前，您需要：
一个有权限创建 AWS Cognito 用户池的 AWS 账户
对 AWS Cognito 概念（用户池、应用程序客户端）的基本了解
您的 FastMCP 服务器 URL（开发环境可以是 localhost，例如 http://localhost:8000）

步骤 1：创建 AWS Cognito 用户池和应用程序客户端
设置带有应用程序客户端的 AWS Cognito 用户池以获取身份验证所需的凭据：
1

导航到 AWS Cognito

转到 AWS Cognito 控制台 并确保您在所需的 AWS 区域中。
从侧边导航中选择 “用户池”（如果看不到，请点击左上角的汉堡包图标），然后点击 “创建用户池” 创建新的用户池。
2

定义您的应用程序

AWS Cognito 现在提供简化的设置体验：
应用程序类型: 选择 “传统 Web 应用程序”（这是 FastMCP 服务器端身份验证的正确选择）
为您的应用程序命名: 输入描述性名称（例如，FastMCP Server）
传统 Web 应用程序类型自动配置：
带有客户端密钥的服务器端身份验证
授权码授予流程
适合机密客户端的安全设置
选择“传统 Web 应用程序”而不是 SPA、移动应用程序或机器对机器选项。这确保了 FastMCP 的正确 OAuth 2.0 配置。
3

配置选项

AWS 将引导您完成配置选项：
登录标识符: 选择用户登录方式（电子邮件、用户名或电话）
必需属性: 选择您需要的任何附加用户信息
返回 URL: 添加您的回调 URL（例如，开发环境为 http://localhost:8000/auth/callback）
简化界面根据您的应用程序类型选择自动处理大多数 OAuth 安全设置。
4

审查和创建

审查您的配置并点击 “创建用户池”。
创建后，您将看到用户池详细信息。保存这些重要值：
用户池 ID（格式：eu-central-1_XXXXXXXXX）
客户端 ID（在侧边导航中找到 → “应用程序” → “应用程序客户端” → <您的应用程序名称，例如 FastMCP Server> → “应用程序客户端信息”）
客户端密钥（在侧边导航中找到 → “应用程序” → “应用程序客户端” → <您的应用程序名称，例如 FastMCP Server> → “应用程序客户端信息”）
用户池 ID 和应用程序客户端凭据是 FastMCP 配置所需的全部。
5

配置 OAuth 设置

在您的应用程序客户端设置中的“登录页面”下，您可以双重检查并调整 OAuth 配置：
允许的回调 URL: 添加您的服务器 URL + /auth/callback（例如，http://localhost:8000/auth/callback）
允许的注销 URL: 可选，用于注销功能
OAuth 2.0 授予类型: 确保选择了“授权码授予”
OpenID Connect 作用域: 选择您的应用程序需要的作用域（例如，openid、email、profile）
对于本地开发，您可以使用 http://localhost URL。对于生产环境，您必须使用 HTTPS。
6

配置资源服务器

AWS Cognito 需要配置资源服务器条目才能支持带受保护资源的 OAuth。否则令牌交换会以 invalid_grant 错误失败。
在侧边导航中转到 “品牌” → “域名”，然后：
点击 “创建资源服务器”
资源服务器名称：输入一个描述性名称（例如 My MCP Server）
资源服务器标识符：精确输入 MCP 端点的访问 URL（开发环境可用 http://localhost:8000/mcp，生产环境如 https://your-server.com/mcp）
点击 “创建资源服务器”
资源服务器标识符必须与 base_url + mcp_path 完全一致。默认配置中 base_url="http://localhost:8000" 且 path="/mcp"，请使用 http://localhost:8000/mcp。
7

保存您的凭据

设置后，您将拥有：
用户池 ID: 格式似 eu-central-1_XXXXXXXXX
客户端 ID: 您应用程序的客户端标识符
客户端密钥: 生成的客户端密钥（保持安全）
AWS 区域: 您的 AWS Cognito 用户池所在的位置
安全存储这些凭据。永远不要将它们提交到版本控制。在生产环境中使用环境变量或 AWS Secrets Manager。

步骤 2：FastMCP 配置
使用 AWSCognitoProvider 创建您的 FastMCP 服务器，它自动处理 AWS Cognito 的 JWT 令牌和用户声明：
server.py
from fastmcp import FastMCP
from fastmcp.server.auth.providers.aws import AWSCognitoProvider
from fastmcp.server.dependencies import get_access_token

# AWSCognitoProvider 处理 JWT 验证和用户声明
auth_provider = AWSCognitoProvider(
    user_pool_id="eu-central-1_XXXXXXXXX",   # 您的 AWS Cognito 用户池 ID
    aws_region="eu-central-1",               # AWS 区域（默认为 eu-central-1）
    client_id="your-app-client-id",          # 您的应用程序客户端 ID
    client_secret="your-app-client-secret",  # 您的应用程序客户端密钥
    base_url="http://localhost:8000",        # 必须与您的回调 URL 匹配
    # redirect_path="/auth/callback"         # 默认值，如需要可自定义
)

mcp = FastMCP(name="AWS Cognito Secured App", auth=auth_provider)

# 添加一个受保护的工具来测试身份验证
@mcp.tool
async def get_access_token_claims() -> dict:
    """获取已认证用户的访问令牌声明。"""
    token = get_access_token()
    return {
        "sub": token.claims.get("sub"),
        "username": token.claims.get("username"),
        "cognito:groups": token.claims.get("cognito:groups", []),
    }

测试

运行服务器
使用 HTTP 传输启动您的 FastMCP 服务器以启用 OAuth 流程：
fastmcp run server.py --transport http --port 8000

您的服务器现在正在运行并受到 AWS Cognito OAuth 身份验证保护。

使用客户端测试
创建一个与您的 AWS Cognito 保护服务器进行身份验证的测试客户端：
test_client.py
from fastmcp import Client
import asyncio

async def main():
    # 客户端将自动处理 AWS Cognito OAuth
    async with Client("http://localhost:8000/mcp/", auth="oauth") as client:
        # 首次连接将在您的浏览器中打开 AWS Cognito 登录页面
        print("✓ 已通过 AWS Cognito 认证！")

        # 测试受保护的工具
        print("调用受保护的工具：get_access_token_claims")
        result = await client.call_tool("get_access_token_claims")
        user_data = result.data
        print("可用的访问令牌声明：")
        print(f"- sub: {user_data.get('sub', 'N/A')}")
        print(f"- username: {user_data.get('username', 'N/A')}")
        print(f"- cognito:groups: {user_data.get('cognito:groups', [])}")

if __name__ == "__main__":
    asyncio.run(main())

当您首次运行客户端时：
您的浏览器将打开 AWS Cognito 的托管 UI 登录页面
登录（或注册）后，您将被重定向回您的 MCP 服务器
客户端接收 JWT 令牌并可以进行身份验证请求
客户端在本地缓存令牌，因此除非令牌过期或您明确清除缓存，否则后续运行时无需重新认证。

生产环境配置
版本
2.13.0
新增
在生产部署中，为了在服务器重启后仍能保留令牌，请同时配置 jwt_signing_key 和 client_storage：
server.py
import os
from fastmcp import FastMCP
from fastmcp.server.auth.providers.aws import AWSCognitoProvider
from key_value.aio.stores.redis import RedisStore
from key_value.aio.wrappers.encryption import FernetEncryptionWrapper
from cryptography.fernet import Fernet

# 使用加密的持久化令牌存储进行生产配置
auth_provider = AWSCognitoProvider(
    user_pool_id="eu-central-1_XXXXXXXXX",
    aws_region="eu-central-1",
    client_id="your-app-client-id",
    client_secret="your-app-client-secret",
    base_url="https://your-production-domain.com",

    # 生产环境令牌管理
    jwt_signing_key=os.environ["JWT_SIGNING_KEY"],
    client_storage=FernetEncryptionWrapper(
        key_value=RedisStore(
            host=os.environ["REDIS_HOST"],
            port=int(os.environ["REDIS_PORT"])
        ),
        fernet=Fernet(os.environ["STORAGE_ENCRYPTION_KEY"])
    )
)

mcp = FastMCP(name="Production AWS Cognito App", auth=auth_provider)

jwt_signing_key 与 client_storage 需要配合使用，才能在服务器重启后保留令牌与客户端注册信息。请使用 FernetEncryptionWrapper 加密存储中的敏感 OAuth 令牌，否则将以明文保存。建议通过环境变量传递密钥，并使用 Redis 等持久化后端以便适配分布式部署。
更多参数细节请参阅 OAuth 代理文档。

环境变量
对于生产部署，使用环境变量而不是硬编码凭据。

提供者选择
设置此环境变量允许自动使用 AWS Cognito 提供者，而无需在代码中显式实例化它。

FASTMCP_SERVER_AUTH
默认值:"Not set"
设置为 fastmcp.server.auth.providers.aws.AWSCognitoProvider 以使用 AWS Cognito 身份验证。

AWS Cognito 特定配置
这些环境变量为 AWS Cognito 提供者提供默认值，无论是手动实例化还是通过 FASTMCP_SERVER_AUTH 配置。

FASTMCP_SERVER_AUTH_AWS_COGNITO_USER_POOL_ID
必填
您的 AWS Cognito 用户池 ID（例如，eu-central-1_XXXXXXXXX）

FASTMCP_SERVER_AUTH_AWS_COGNITO_AWS_REGION
默认值:"eu-central-1"
您的 AWS Cognito 用户池所在的 AWS 区域

FASTMCP_SERVER_AUTH_AWS_COGNITO_CLIENT_ID
必填
您的 AWS Cognito 应用程序客户端 ID

FASTMCP_SERVER_AUTH_AWS_COGNITO_CLIENT_SECRET
必填
您的 AWS Cognito 应用程序客户端密钥

FASTMCP_SERVER_AUTH_AWS_COGNITO_BASE_URL
默认值:"http://localhost:8000"
OAuth 端点可访问的公共 URL（包含任何挂载路径）

FASTMCP_SERVER_AUTH_AWS_COGNITO_ISSUER_URL
默认值:"使用 BASE_URL"
用于 OAuth 元数据的 Issuer URL（默认等于 BASE_URL）。当挂载在路径前缀下时，请设置为根级 URL 以避免 404 日志。详见 HTTP 部署指南。

FASTMCP_SERVER_AUTH_AWS_COGNITO_REDIRECT_PATH
默认值:"/auth/callback"
在您的 AWS Cognito 应用程序客户端中配置的重定向路径之一

FASTMCP_SERVER_AUTH_AWS_COGNITO_REQUIRED_SCOPES
默认值:"[\"openid\"]"
逗号、空格或 JSON 分隔的所需 OAuth 作用域列表（例如，openid email 或 ["openid","email","profile"]）
Example .env file:
# 使用 AWS Cognito 提供者
FASTMCP_SERVER_AUTH=fastmcp.server.auth.providers.aws.AWSCognitoProvider

# AWS Cognito 凭据
FASTMCP_SERVER_AUTH_AWS_COGNITO_USER_POOL_ID=eu-central-1_XXXXXXXXX
FASTMCP_SERVER_AUTH_AWS_COGNITO_AWS_REGION=eu-central-1
FASTMCP_SERVER_AUTH_AWS_COGNITO_CLIENT_ID=your-app-client-id
FASTMCP_SERVER_AUTH_AWS_COGNITO_CLIENT_SECRET=your-app-client-secret
FASTMCP_SERVER_AUTH_AWS_COGNITO_BASE_URL=https://your-server.com
FASTMCP_SERVER_AUTH_AWS_COGNITO_REQUIRED_SCOPES=openid,email,profile

With environment variables set, your server code simplifies to:
server.py
from fastmcp import FastMCP
from fastmcp.server.dependencies import get_access_token

# 身份验证自动从环境中配置
mcp = FastMCP(name="AWS Cognito Secured App")

@mcp.tool
async def get_access_token_claims() -> dict:
    """获取已认证用户的访问令牌声明。"""
    token = get_access_token()
    return {
        "sub": token.claims.get("sub"),
        "username": token.claims.get("username"),
        "cognito:groups": token.claims.get("cognito:groups", []),
    }

功能

JWT 令牌验证
AWS Cognito 提供者包括强大的 JWT 令牌验证：
签名验证: 根据 AWS Cognito 的公钥 (JWKS) 验证令牌
过期检查: 自动拒绝已过期的令牌
颁发者验证: 确保令牌来自您特定的 AWS Cognito 用户池
作用域强制: 验证所需的 OAuth 作用域是否存在

用户声明和组
从 AWS Cognito JWT 令牌中访问丰富的用户信息：
from fastmcp.server.dependencies import get_access_token

@mcp.tool
async def admin_only_tool() -> str:
    """仅对管理员用户可用的工具。"""
    token = get_access_token()
    user_groups = token.claims.get("cognito:groups", [])

    if "admin" not in user_groups:
        raise ValueError("此工具需要管理员访问权限")

    return "管理员访问权限已授予！"

企业集成
适合企业环境：
单一登录 (SSO): 与企业身份提供者集成
多因素身份验证 (MFA): 利用 AWS Cognito 的内置 MFA
用户组: 通过 AWS Cognito 组进行基于角色的访问控制
自定义属性: 访问在您的 AWS Cognito 用户池中定义的自定义用户属性
合规性: 满足企业安全和合规性要求
AuthKit 🤝 FastMCP

Azure（Microsoft Entra ID）OAuth 🤝 FastMCP

x

---

配置
先决条件
第 1 步：创建 Azure 应用注册
第 2 步：FastMCP 配置
测试
运行服务器
使用客户端测试
生产环境配置
环境变量
提供程序选择
Azure 特定配置
认证
Azure（Microsoft Entra ID）OAuth 🤝 FastMCP

使用 Azure/Microsoft Entra OAuth 保护您的 FastMCP 服务器

版本
2.13.0
新增
本指南向您展示如何使用 Azure OAuth（Microsoft Entra ID）保护 FastMCP 服务器。由于 Azure 不支持动态客户端注册，此集成采用 OAuth 代理 模式，将 Azure 的传统 OAuth 与 MCP 的身份验证需求衔接起来。FastMCP 会基于应用程序的 client_id 验证 Azure JWT。

配置

先决条件
在开始之前，您需要：
一个具有创建应用注册权限的 Azure 账户
您的 FastMCP 服务器 URL（开发时可以是 localhost，例如 http://localhost:8000）
您的 Azure 租户 ID（在 Azure 门户的 Microsoft Entra ID 下可以找到）

第 1 步：创建 Azure 应用注册
在 Azure 门户中创建应用注册以获取身份验证所需的凭据：
1

导航到应用注册

转到 Azure 门户并导航到 Microsoft Entra ID → 应用注册。
点击 “新注册” 创建新应用程序。
2

配置您的应用程序

填写应用程序详细信息：
名称：选择用户可以识别的名称（例如，“我的 FastMCP 服务器”）
支持的账户类型：根据您的需要选择：
单租户：仅您组织中的用户
多租户：任何 Microsoft Entra 目录中的用户
多租户 + 个人账户：任何 Microsoft 账户
重定向 URI：选择 “Web” 并输入您的服务器 URL + /auth/callback（例如，http://localhost:8000/auth/callback）
重定向 URI 必须完全匹配。默认路径是 /auth/callback，但您可以使用 redirect_path 参数进行自定义。对于本地开发，Azure 允许 http://localhost URL。对于生产环境，您必须使用 HTTPS。
如果您想使用自定义回调路径（例如，/auth/azure/callback），请确保在 Azure 应用注册和配置 AzureProvider 时的 redirect_path 参数中设置相同的路径。
公开 API：配置您的应用程序 ID URI 并定义作用域
在应用注册侧边栏中转到公开 API。
点击“应用程序 ID URI”旁边的设置，并选择以下之一：
保持默认的 api://{client_id}
设置自定义值，遵循支持的格式（参见 标识符 URI 限制）
点击添加作用域并创建您的应用程序所需的作用域，例如：
作用域名称：read（或 write 等）
管理员同意显示名称/描述：适合您的组织
谁可以同意：根据需要（仅管理员或管理员和用户）
配置访问令牌版本：确保您的应用程序使用访问令牌 v2
在应用注册侧边栏中转到清单。
找到 requestedAccessTokenVersion 属性并将其设置为 2：
"api": {
    "requestedAccessTokenVersion": 2
}

在清单编辑器顶部点击保存。
FastMCP 的 Azure 集成需要访问令牌 v2 才能正常工作。如果未设置，您可能会遇到身份验证错误。
在 FastMCP 的 AzureProvider 中，将 identifier_uri 设置为您的应用程序 ID URI（可选；默认为 api://{client_id}），并将 required_scopes 设置为未加前缀的作用域名称（例如 read、write）。授权流程中，FastMCP 会自动为作用域补上 identifier_uri 前缀。
3

创建客户端密钥

注册后，在您的应用设置中导航到 证书和密钥。
点击 “新客户端密钥”
添加描述（例如，“FastMCP 服务器”）
选择过期时间
点击 “添加”
立即复制密钥值 - 它不会再次显示！如果丢失，您需要创建新的密钥。
4

记录您的凭据

从您的应用注册的 概览 页面，记录：
应用程序（客户端）ID：像 835f09b6-0f0f-40cc-85cb-f32c5829a149 这样的 UUID
目录（租户）ID：像 08541b6e-646d-43de-a0eb-834e6713d6d5 这样的 UUID
客户端密钥：您在上一步中复制的值
安全存储这些凭据。永远不要将它们提交到版本控制。在生产环境中使用环境变量或密钥管理器。

第 2 步：FastMCP 配置
使用 AzureProvider 创建您的 FastMCP 服务器，它会自动处理 Azure 的 OAuth 流程：
server.py
from fastmcp import FastMCP
from fastmcp.server.auth.providers.azure import AzureProvider

# AzureProvider 处理 Azure 的令牌格式和验证
auth_provider = AzureProvider(
    client_id="835f09b6-0f0f-40cc-85cb-f32c5829a149",  # 您的 Azure 应用客户端 ID
    client_secret="your-client-secret",                 # 您的 Azure 应用客户端密钥
    tenant_id="08541b6e-646d-43de-a0eb-834e6713d6d5", # 您的 Azure 租户 ID（必需）
    base_url="http://localhost:8000",                   # 必须与您的应用注册匹配
    required_scopes=["your-scope"],                 # 必填：至少一个作用域，来自您的应用注册
    # identifier_uri 默认为 api://{client_id}
    # identifier_uri="api://your-api-id",
    # 可选：在授权请求中请求附加的上游作用域
    # additional_authorize_scopes=["User.Read", "offline_access", "openid", "email"],
    # redirect_path="/auth/callback"                  # Default value, customize if needed
)

mcp = FastMCP(name="Azure Secured App", auth=auth_provider)

# 添加受保护的工具来测试身份验证
@mcp.tool
async def get_user_info() -> dict:
    """返回经过身份验证的 Azure 用户信息。"""
    from fastmcp.server.dependencies import get_access_token
    
    token = get_access_token()
    # AzureProvider 在令牌声明中存储用户数据
    return {
        "azure_id": token.claims.get("sub"),
        "email": token.claims.get("email"),
        "name": token.claims.get("name"),
        "job_title": token.claims.get("job_title"),
        "office_location": token.claims.get("office_location")
    }

重要：tenant_id 参数是 必需的。由于安全要求，Azure 不再支持为新应用程序使用 “common”。您必须使用以下之一：
您的特定租户 ID：在 Azure 门户中找到（例如，08541b6e-646d-43de-a0eb-834e6713d6d5）
“organizations”：仅限工作和学校账户
“consumers”：仅限个人 Microsoft 账户
推荐使用您的特定租户 ID 以获得更好的安全性和控制。
重要：required_scopes 参数是必填项，必须至少包含一个作用域。Azure 的 OAuth API 要求每个授权请求都带上 scope 参数，否则无法完成认证。请使用 Azure 应用注册中“公开 API”所创建的未加前缀作用域名称（例如 ["read", "write"]）。

测试

运行服务器
使用 HTTP 传输启动您的 FastMCP 服务器以启用 OAuth 流程：
fastmcp run server.py --transport http --port 8000

您的服务器现在正在运行并受到 Azure OAuth 身份验证的保护。

使用客户端测试
创建一个与您的 Azure 保护服务器进行身份验证的测试客户端：
test_client.py
from fastmcp import Client
import asyncio

async def main():
    # 客户端将自动处理 Azure OAuth
    async with Client("http://localhost:8000/mcp/", auth="oauth") as client:
        # 首次连接将在浏览器中打开 Azure 登录
        print("✓ 已通过 Azure 身份验证！")
        
        # 测试受保护的工具
        result = await client.call_tool("get_user_info")
        print(f"Azure 用户：{result['email']}")
        print(f"姓名：{result['name']}")

if __name__ == "__main__":
    asyncio.run(main())

当您首次运行客户端时：
您的浏览器将打开 Microsoft 的授权页面
使用您的 Microsoft 账户登录（根据您的租户配置，可以是工作、学校或个人账户）
授予请求的权限
授权后，您将被重定向回去
客户端接收令牌并可以发出经过身份验证的请求
客户端在本地缓存令牌，因此除非令牌过期或您显式清除缓存，否则您无需为后续运行重新进行身份验证。

生产环境配置
版本
2.13.0
新增
在生产部署中，为了在服务器重启后仍能保留令牌，请同时配置 jwt_signing_key 与 client_storage：
server.py
import os
from fastmcp import FastMCP
from fastmcp.server.auth.providers.azure import AzureProvider
from key_value.aio.stores.redis import RedisStore
from key_value.aio.wrappers.encryption import FernetEncryptionWrapper
from cryptography.fernet import Fernet

# 使用加密的持久化令牌存储进行生产配置
auth_provider = AzureProvider(
    client_id="835f09b6-0f0f-40cc-85cb-f32c5829a149",
    client_secret="your-client-secret",
    tenant_id="08541b6e-646d-43de-a0eb-834e6713d6d5",
    base_url="https://your-production-domain.com",
    required_scopes=["your-scope"],

    # 生产环境令牌管理
    jwt_signing_key=os.environ["JWT_SIGNING_KEY"],
    client_storage=FernetEncryptionWrapper(
        key_value=RedisStore(
            host=os.environ["REDIS_HOST"],
            port=int(os.environ["REDIS_PORT"])
        ),
        fernet=Fernet(os.environ["STORAGE_ENCRYPTION_KEY"])
    )
)

mcp = FastMCP(name="Production Azure App", auth=auth_provider)

jwt_signing_key 与 client_storage 需要配合使用，才能在服务器重启后保留令牌与客户端注册信息。请使用 FernetEncryptionWrapper 加密存储中的敏感 OAuth 令牌，否则将以明文保存。建议将密钥存放在环境变量中，并选择 Redis 等持久化后端以支持分布式部署。
更多参数说明请参阅 OAuth 代理文档。

环境变量
版本
2.12.1
新增
对于生产部署，请使用环境变量而不是硬编码凭据。

提供程序选择
设置此环境变量允许自动使用 Azure 提供程序，而无需在代码中显式实例化它。

FASTMCP_SERVER_AUTH
默认值:"未设置"
设置为 fastmcp.server.auth.providers.azure.AzureProvider 以使用 Azure 身份验证。

Azure 特定配置
这些环境变量为 Azure 提供程序提供默认值，无论它是手动实例化的还是通过 FASTMCP_SERVER_AUTH 配置的。

FASTMCP_SERVER_AUTH_AZURE_CLIENT_ID
必填
您的 Azure 应用注册客户端 ID（例如，835f09b6-0f0f-40cc-85cb-f32c5829a149）

FASTMCP_SERVER_AUTH_AZURE_CLIENT_SECRET
必填
您的 Azure 应用注册客户端密钥

FASTMCP_SERVER_AUTH_AZURE_TENANT_ID
必填
您的 Azure 租户 ID（特定 ID、“organizations” 或 “consumers”）
这是 必需的。在 Azure 门户的 Microsoft Entra ID → 概览 下找到您的租户 ID。

FASTMCP_SERVER_AUTH_AZURE_BASE_URL
默认值:"http://localhost:8000"
OAuth 端点可访问的公共 URL（包含任何挂载路径）

FASTMCP_SERVER_AUTH_AZURE_ISSUER_URL
默认值:"使用 BASE_URL"
用于 OAuth 元数据的 Issuer URL（默认等于 BASE_URL）。当挂载在路径前缀下时，请设置为根级 URL 以避免 404 日志。详见 HTTP 部署指南。

FASTMCP_SERVER_AUTH_AZURE_REDIRECT_PATH
默认值:"/auth/callback"
在您的 Azure 应用注册中配置的重定向路径

FASTMCP_SERVER_AUTH_AZURE_REQUIRED_SCOPES
必填
您的 API 所需的作用域列表，使用逗号、空格或 JSON 分隔（至少需要一个作用域）。这些作用域会在令牌上验证，并在客户端未请求特定作用域时作为默认值。请使用应用注册中未加前缀的作用域名称（例如 read,write）。
Azure 的 OAuth API 要求提供 scope 参数——必须至少配置一个作用域。

FASTMCP_SERVER_AUTH_AZURE_ADDITIONAL_AUTHORIZE_SCOPES
默认值:""
在授权请求中包含的附加作用域的逗号、空格或 JSON 分隔列表，不加前缀。使用此功能请求上游作用域，如 Microsoft Graph 权限。这些不用于令牌验证。

FASTMCP_SERVER_AUTH_AZURE_IDENTIFIER_URI
默认值:"api://{client_id}"
在授权期间用于为作用域加前缀的应用程序 ID URI。
示例 .env 文件：
# 使用 Azure 提供程序
FASTMCP_SERVER_AUTH=fastmcp.server.auth.providers.azure.AzureProvider

# Azure OAuth 凭据
FASTMCP_SERVER_AUTH_AZURE_CLIENT_ID=835f09b6-0f0f-40cc-85cb-f32c5829a149
FASTMCP_SERVER_AUTH_AZURE_CLIENT_SECRET=your-client-secret-here
FASTMCP_SERVER_AUTH_AZURE_TENANT_ID=08541b6e-646d-43de-a0eb-834e6713d6d5
FASTMCP_SERVER_AUTH_AZURE_BASE_URL=https://your-server.com
FASTMCP_SERVER_AUTH_AZURE_REQUIRED_SCOPES=read,write
# 可选自定义API配置
# FASTMCP_SERVER_AUTH_AZURE_IDENTIFIER_URI=api://your-api-id
# 请求附加上游范围（可选）
# FASTMCP_SERVER_AUTH_AZURE_ADDITIONAL_AUTHORIZE_SCOPES=User.Read,Mail.Read

设置环境变量后，您的服务器代码简化为：
server.py
from fastmcp import FastMCP

# 身份验证从环境中自动配置
mcp = FastMCP(name="Azure 安全应用")

@mcp.tool
async def protected_tool(query: str) -> str:
    """需要 Azure 身份验证才能访问的工具。"""
    # 您的工具实现在这里
    return f"处理经过身份验证的请求：{query}"

AWS Cognito OAuth 🤝 FastMCP

Descope 🤝 FastMCP

x

---

配置
前置条件
第 1 步：配置 Descope
第 2 步：环境设置
第 3 步：FastMCP 配置
测试
生产配置
认证
Descope 🤝 FastMCP

使用 Descope 保护你的 FastMCP 服务端

版本
2.12.4
新增
本指南展示如何使用 Descope 保护你的 FastMCP 服务端。Descope 是完整的身份认证和用户管理解决方案。该集成使用 Remote OAuth 模式，由 Descope 处理用户登录，而你的 FastMCP 服务端负责验证令牌。

配置

前置条件
开始之前，你需要：
注册 一个永久免费 Descope 账号
你的 FastMCP 服务端 URL（开发时可以是 localhost，例如 http://localhost:3000）

第 1 步：配置 Descope
1

创建 MCP 服务端

前往 Descope Console 的 MCP Servers 页面，创建一个新的 MCP Server。
为 MCP 服务端填写名称和描述。
确保已启用 Dynamic Client Registration (DCR)。然后点击 Create。
创建 MCP Server 后，记下你的 Well-Known URL。
FastMCP 客户端要自动注册到你的身份认证服务端，必须启用 DCR。
2

记录你的 Well-Known URL

从 MCP Server Settings 保存你的 Well-Known URL：
Well-Known URL: https://.../v1/apps/agentic/P.../M.../.well-known/openid-configuration

第 2 步：环境设置
创建包含 Descope 配置的 .env 文件：
DESCOPE_CONFIG_URL=https://.../v1/apps/agentic/P.../M.../.well-known/openid-configuration     # 你的 Descope Well-Known URL
SERVER_URL=http://localhost:3000     # 你的服务端基础 URL

第 3 步：FastMCP 配置
创建 FastMCP 服务端文件，并使用 DescopeProvider 自动处理全部 OAuth 集成：
server.py
from fastmcp import FastMCP
from fastmcp.server.auth.providers.descope import DescopeProvider

# DescopeProvider 会自动发现 Descope 端点
# 并配置 JWT 令牌验证
auth_provider = DescopeProvider(
    config_url="https://.../.well-known/openid-configuration",  # 你的 MCP Server .well-known URL
    base_url=SERVER_URL,                                        # 你的服务端公共 URL
)

# 创建带身份认证的 FastMCP 服务端
mcp = FastMCP(name="My Descope Protected Server", auth=auth_provider)

测试
要测试服务端，可以使用 fastmcp CLI 在本地运行它。假设你已将上述代码保存为 server.py（并将环境变量替换为实际值！），可以运行以下命令：
fastmcp run server.py --transport http --port 8000

现在，你可以使用 FastMCP 客户端测试在完成身份认证后是否能访问服务端：
from fastmcp import Client
import asyncio

async def main():
    async with Client("http://localhost:8000/mcp", auth="oauth") as client:
        assert await client.ping()

if __name__ == "__main__":
    asyncio.run(main())

生产配置
对于生产部署，请从环境变量加载配置：
server.py
import os
from fastmcp import FastMCP
from fastmcp.server.auth.providers.descope import DescopeProvider

# 从环境变量加载配置
auth = DescopeProvider(
    config_url=os.environ.get("DESCOPE_CONFIG_URL"),
    base_url=os.environ.get("BASE_URL", "https://your-server.com")
)

mcp = FastMCP(name="My Descope Protected Server", auth=auth)

Azure（Microsoft Entra ID）OAuth 🤝 FastMCP

Discord OAuth 🤝 FastMCP

x

---

配置
前置条件
第 1 步：创建 Discord 应用
第 2 步：FastMCP 配置
测试
运行服务端
使用客户端测试
Discord Scopes
生产配置
认证
Discord OAuth 🤝 FastMCP

使用 Discord OAuth 保护你的 FastMCP 服务端

版本
2.13.2
新增
本指南展示如何使用 Discord OAuth 保护你的 FastMCP 服务端。由于 Discord 不支持 Dynamic Client Registration，此集成使用 OAuth Proxy 模式，将 Discord 的传统 OAuth 与 MCP 的身份认证要求连接起来。

配置

前置条件
开始之前，你需要：
一个可创建应用的 Discord 账号
你的 FastMCP 服务端 URL（开发时可以是 localhost，例如 http://localhost:8000）

第 1 步：创建 Discord 应用
在 Discord Developer Portal 中创建应用，以获取身份认证所需的凭据：
1

前往 Discord Developer Portal

前往 Discord Developer Portal。
点击 “New Application”，并为其填写一个用户能识别的名称（例如 “My FastMCP Server”）。
2

配置 OAuth2 设置

在左侧边栏中点击 “OAuth2”。
在 Redirects 部分点击 “Add Redirect”，并输入你的回调 URL：
开发环境：http://localhost:8000/auth/callback
生产环境：https://your-domain.com/auth/callback
重定向 URL 必须完全匹配。默认路径是 /auth/callback，但你可以使用 redirect_path 参数自定义它。Discord 允许在开发环境使用 http://localhost URL。生产环境请使用 HTTPS。
3

保存你的凭据

在同一个 OAuth2 页面中，你会看到：
Client ID：类似 12345 的数字字符串
Client Secret：点击 “Reset Secret” 生成
请安全保存这些凭据。切勿将它们提交到版本控制中。生产环境请使用环境变量或密钥管理器。

第 2 步：FastMCP 配置
使用 DiscordProvider 创建 FastMCP 服务端，它会自动处理 Discord 的 OAuth 流程：
server.py
from fastmcp import FastMCP
from fastmcp.server.auth.providers.discord import DiscordProvider

auth_provider = DiscordProvider(
    client_id="12345",      # 你的 Discord Application Client ID
    client_secret="your-client-secret",    # 你的 Discord OAuth Client Secret
    base_url="http://localhost:8000",      # 必须与你的 OAuth 配置匹配
)

mcp = FastMCP(name="Discord Secured App", auth=auth_provider)

@mcp.tool
async def get_user_info() -> dict:
    """返回已认证 Discord 用户的信息。"""
    from fastmcp.server.dependencies import get_access_token

    token = get_access_token()
    return {
        "discord_id": token.claims.get("sub"),
        "username": token.claims.get("username"),
        "avatar": token.claims.get("avatar"),
    }

测试

运行服务端
使用 HTTP 传输启动 FastMCP 服务端，以启用 OAuth 流程：
fastmcp run server.py --transport http --port 8000

你的服务端现在已经运行，并受到 Discord OAuth 身份认证保护。

使用客户端测试
创建一个测试客户端，对受 Discord 保护的服务端进行身份认证：
test_client.py
from fastmcp import Client
import asyncio

async def main():
    async with Client("http://localhost:8000/mcp", auth="oauth") as client:
        print("✓ Authenticated with Discord!")

        result = await client.call_tool("get_user_info")
        print(f"Discord user: {result['username']}")

if __name__ == "__main__":
    asyncio.run(main())

首次运行客户端时：
浏览器会打开 Discord 的授权页面
使用 Discord 账号登录并授权该应用
授权后，你会被重定向回来
客户端会收到令牌，并可以发起已认证的请求
客户端会在本地缓存令牌，因此后续运行时无需重新认证，除非令牌过期或你显式清空缓存。

Discord Scopes
Discord OAuth 支持多个 scope，用于访问不同类型的用户数据：
Scope	描述
identify	访问用户名、头像和 discriminator（默认）
email	访问用户的电子邮件地址
guilds	访问用户的服务器列表
guilds.join	将用户添加到服务器的能力
要请求额外 scope：
auth_provider = DiscordProvider(
    client_id="...",
    client_secret="...",
    base_url="http://localhost:8000",
    required_scopes=["identify", "email"],
)

生产配置
对于需要在服务端重启后保留令牌管理状态的生产部署，请配置 jwt_signing_key 和 client_storage：
server.py
import os
from fastmcp import FastMCP
from fastmcp.server.auth.providers.discord import DiscordProvider
from key_value.aio.stores.redis import RedisStore
from key_value.aio.wrappers.encryption import FernetEncryptionWrapper
from cryptography.fernet import Fernet

auth_provider = DiscordProvider(
    client_id="12345",
    client_secret=os.environ["DISCORD_CLIENT_SECRET"],
    base_url="https://your-production-domain.com",

    jwt_signing_key=os.environ["JWT_SIGNING_KEY"],
    client_storage=FernetEncryptionWrapper(
        key_value=RedisStore(
            host=os.environ["REDIS_HOST"],
            port=int(os.environ["REDIS_PORT"])
        ),
        fernet=Fernet(os.environ["STORAGE_ENCRYPTION_KEY"])
    )
)

mcp = FastMCP(name="Production Discord App", auth=auth_provider)

参数（jwt_signing_key 和 client_storage）会协同工作，确保令牌和客户端注册在服务端重启后仍然保留。请使用 FernetEncryptionWrapper 包装存储，以加密静态敏感 OAuth 令牌，否则令牌会以明文存储。请将密钥存入环境变量，并在分布式部署中使用 Redis 等持久化存储后端。
有关这些参数的完整详情，请参阅 OAuth Proxy 文档。
Descope 🤝 FastMCP

Eunomia Authorization 🤝 FastMCP

x

---

工作原理
列表操作
执行操作
为服务端添加授权
创建带授权的服务端
配置访问策略
运行服务端
认证
Eunomia Authorization 🤝 FastMCP

使用 Eunomia 为 FastMCP 服务端添加基于策略的授权

只需添加一行代码，即可通过 Eunomia 授权中间件为 FastMCP 服务端加入基于策略的授权。
控制 MCP 客户端可以在你的服务端查看和执行哪些工具、资源和提示词。你可以定义基于 JSON 的动态策略，并获取所有访问尝试和违规行为的完整审计日志。

工作原理
借助 FastMCP 的中间件，Eunomia 中间件会拦截发送到服务端的所有 MCP 请求，并自动将 MCP 方法映射到授权检查。

列表操作
对于列表操作（tools/list、resources/list、prompts/list），该中间件会像过滤器一样工作，向客户端隐藏未被定义策略授权的组件。
Eunomia Server
FastMCP Server
Eunomia Middleware
MCP Client
Eunomia Server
FastMCP Server
Eunomia Middleware
MCP Client
MCP Listing Request (e.g., tools/list)
MCP Listing Request
MCP Listing Response
Authorization Checks
Authorization Decisions
Filtered MCP Listing Response

执行操作
对于执行操作（tools/call、resources/read、prompts/get），该中间件会像防火墙一样工作，阻止未被定义策略授权的操作。
Eunomia Server
FastMCP Server
Eunomia Middleware
MCP Client
Eunomia Server
FastMCP Server
Eunomia Middleware
MCP Client
MCP Execution Request (e.g., tools/call)
Authorization Check
Authorization Decision
MCP Unauthorized Error (if denied)
MCP Execution Request (if allowed)
MCP Execution Response (if allowed)
MCP Execution Response (if allowed)

为服务端添加授权
Eunomia 是面向 AI 的授权服务端，用于处理策略决策。默认情况下，该服务端会嵌入到你的 MCP 服务端中运行，实现零配置；也可以远程运行，以集中处理策略决策。

创建带授权的服务端
首先，安装 eunomia-mcp 包：
pip install eunomia-mcp

然后创建 FastMCP 服务端，并用一行代码添加 Eunomia 中间件：
server.py
from fastmcp import FastMCP
from eunomia_mcp import create_eunomia_middleware

# 创建 FastMCP 服务端
mcp = FastMCP("Secure MCP Server 🔒")

@mcp.tool()
def add(a: int, b: int) -> int:
    """将两个数字相加"""
    return a + b

# 向服务端添加中间件
middleware = create_eunomia_middleware(policy_file="mcp_policies.json")
mcp.add_middleware(middleware)

if __name__ == "__main__":
    mcp.run()

配置访问策略
在终端中使用 eunomia-mcp CLI 管理授权策略：
# 创建默认策略文件
eunomia-mcp init

# 或者为 FastMCP 服务端创建自定义策略文件
eunomia-mcp init --custom-mcp "app.server:mcp"

这会创建 mcp_policies.json 文件，你可以继续编辑它以满足访问控制需求。
# 编辑完成后，验证策略文件
eunomia-mcp validate mcp_policies.json

运行服务端
像平常一样启动 FastMCP 服务端：
python server.py

现在，中间件会拦截所有 MCP 请求，并根据你的策略进行检查。请求会通过 X-Agent-ID、X-User-ID、User-Agent 或 Authorization 等 header 包含 agent 标识，并自动将 MCP 方法映射到授权资源和动作。
有关详细策略配置、自定义身份认证和远程部署，请访问 Eunomia MCP Middleware 仓库。
Discord OAuth 🤝 FastMCP

GitHub OAuth 🤝 FastMCP

x

---

配置
先决条件
第 1 步：创建 GitHub OAuth 应用
第 2 步：FastMCP 配置
测试
运行服务器
使用客户端测试
生产环境配置
环境变量
提供程序选择
GitHub 特定配置
认证
GitHub OAuth 🤝 FastMCP

使用 GitHub OAuth 保护您的 FastMCP 服务器

版本
2.12.0
新增
本指南将向您展示如何使用 GitHub OAuth 保护您的 FastMCP 服务器。由于 GitHub 不支持动态客户端注册，此集成使用 OAuth 代理 模式来连接 GitHub 的传统 OAuth 与 MCP 的身份验证要求。

配置

先决条件
在开始之前，您需要：
一个具有创建 OAuth 应用权限的 GitHub 账户
您的 FastMCP 服务器 URL（开发时可以是 localhost，例如 http://localhost:8000）

第 1 步：创建 GitHub OAuth 应用
在您的 GitHub 设置中创建 OAuth 应用以获取身份验证所需的凭据：
1

导航到 OAuth 应用

在您的 GitHub 账户中转到 设置 → 开发者设置 → OAuth 应用，或访问 github.com/settings/developers。
点击 “新 OAuth 应用” 创建新应用程序。
2

配置您的 OAuth 应用

填写应用程序详细信息：
应用名称：选择用户可以识别的名称（例如，“我的 FastMCP 服务器”）
主页 URL：您应用程序的主页或文档 URL
授权回调 URL：您的服务器 URL + /auth/callback（例如，http://localhost:8000/auth/callback）
回调 URL 必须完全匹配。默认路径是 /auth/callback，但您可以使用 redirect_path 参数进行自定义。对于本地开发，GitHub 允许 http://localhost URL。对于生产环境，您必须使用 HTTPS。
如果您想使用自定义回调路径（例如，/auth/github/callback），请确保在 GitHub OAuth 应用设置和配置 GitHubProvider 时的 redirect_path 参数中设置相同的路径。
3

保存您的凭据

创建应用后，您将看到：
客户端 ID：像 Ov23liAbcDefGhiJkLmN 这样的公共标识符
客户端密钥：点击 “生成新客户端密钥” 并安全保存该值
安全存储这些凭据。永远不要将它们提交到版本控制。在生产环境中使用环境变量或密钥管理器。

第 2 步：FastMCP 配置
使用 GitHubProvider 创建您的 FastMCP 服务器，它会自动处理 GitHub 的 OAuth 特性：
server.py
from fastmcp import FastMCP
from fastmcp.server.auth.providers.github import GitHubProvider

# GitHubProvider 处理 GitHub 的令牌格式和验证
auth_provider = GitHubProvider(
    client_id="Ov23liAbcDefGhiJkLmN",  # 您的 GitHub OAuth 应用客户端 ID
    client_secret="github_pat_...",     # 您的 GitHub OAuth 应用客户端密钥
    base_url="http://localhost:8000",   # 必须与您的 OAuth 应用配置匹配
    # redirect_path="/auth/callback"   # 默认值，如需可自定义
)

mcp = FastMCP(name="GitHub Secured App", auth=auth_provider)

# 添加受保护的工具来测试身份验证
@mcp.tool
async def get_user_info() -> dict:
    """返回经过身份验证的 GitHub 用户信息。"""
    from fastmcp.server.dependencies import get_access_token
    
    token = get_access_token()
    # GitHubProvider 在令牌声明中存储用户数据
    return {
        "github_user": token.claims.get("login"),
        "name": token.claims.get("name"),
        "email": token.claims.get("email")
    }

测试

运行服务器
使用 HTTP 传输启动您的 FastMCP 服务器以启用 OAuth 流程：
fastmcp run server.py --transport http --port 8000

您的服务器现在正在运行并受到 GitHub OAuth 身份验证的保护。

使用客户端测试
创建一个与您的 GitHub 保护服务器进行身份验证的测试客户端：
test_client.py
from fastmcp import Client
import asyncio

async def main():
    # 客户端将自动处理 GitHub OAuth
    async with Client("http://localhost:8000/mcp/", auth="oauth") as client:
        # 首次连接将在浏览器中打开 GitHub 登录
        print("✓ 已通过 GitHub 身份验证！")
        
        # 测试受保护的工具
        result = await client.call_tool("get_user_info")
        print(f"GitHub 用户：{result['github_user']}")

if __name__ == "__main__":
    asyncio.run(main())

当您首次运行客户端时：
您的浏览器将打开 GitHub 的授权页面
在您授权应用后，您将被重定向回去
客户端接收令牌并可以发出经过身份验证的请求
客户端在本地缓存令牌，因此除非令牌过期或您显式清除缓存，否则您无需为后续运行重新进行身份验证。

生产环境配置
版本
2.13.0
新增
在生产部署中，为了在服务器重启后仍能保留令牌，请同时配置 jwt_signing_key 和 client_storage：
server.py
import os
from fastmcp import FastMCP
from fastmcp.server.auth.providers.github import GitHubProvider
from key_value.aio.stores.redis import RedisStore
from key_value.aio.wrappers.encryption import FernetEncryptionWrapper
from cryptography.fernet import Fernet

# 使用加密的持久化令牌存储进行生产配置
auth_provider = GitHubProvider(
    client_id="Ov23liAbcDefGhiJkLmN",
    client_secret="github_pat_...",
    base_url="https://your-production-domain.com",

    # 生产环境令牌管理
    jwt_signing_key=os.environ["JWT_SIGNING_KEY"],
    client_storage=FernetEncryptionWrapper(
        key_value=RedisStore(
            host=os.environ["REDIS_HOST"],
            port=int(os.environ["REDIS_PORT"])
        ),
        fernet=Fernet(os.environ["STORAGE_ENCRYPTION_KEY"])
    )
)

mcp = FastMCP(name="Production GitHub App", auth=auth_provider)

jwt_signing_key 与 client_storage 需要配合使用，才能在服务器重启后保留令牌与客户端注册信息。请使用 FernetEncryptionWrapper 加密存储中的敏感 OAuth 令牌，否则将以明文保存。建议通过环境变量传递密钥，并使用 Redis 等持久化后端以支持分布式部署。
更多参数详情请参阅 OAuth 代理文档。

环境变量
版本
2.12.1
新增
对于生产部署，请使用环境变量而不是硬编码凭据。

提供程序选择
设置此环境变量允许自动使用 GitHub 提供程序，而无需在代码中显式实例化它。

FASTMCP_SERVER_AUTH
默认值:"未设置"
设置为 fastmcp.server.auth.providers.github.GitHubProvider 以使用 GitHub 身份验证。

GitHub 特定配置
这些环境变量为 GitHub 提供程序提供默认值，无论它是手动实例化的还是通过 FASTMCP_SERVER_AUTH 配置的。

FASTMCP_SERVER_AUTH_GITHUB_CLIENT_ID
必填
您的 GitHub OAuth 应用客户端 ID（例如，Ov23liAbcDefGhiJkLmN）

FASTMCP_SERVER_AUTH_GITHUB_CLIENT_SECRET
必填
您的 GitHub OAuth 应用客户端密钥

FASTMCP_SERVER_AUTH_GITHUB_BASE_URL
默认值:"http://localhost:8000"
OAuth 端点可访问的公共 URL（包含任何挂载路径）

FASTMCP_SERVER_AUTH_GITHUB_ISSUER_URL
默认值:"使用 BASE_URL"
用于 OAuth 元数据的 Issuer URL（默认等于 BASE_URL）。当挂载在路径前缀下时，请设置为根级 URL 以避免 404 日志。详见 HTTP 部署指南。

FASTMCP_SERVER_AUTH_GITHUB_REDIRECT_PATH
默认值:"/auth/callback"
在您的 GitHub OAuth 应用中配置的重定向路径

FASTMCP_SERVER_AUTH_GITHUB_REQUIRED_SCOPES
默认值:"[\"user\"]"
必需的 GitHub 作用域的逗号、空格或 JSON 分隔列表（例如，user repo 或 ["user","repo"]）

FASTMCP_SERVER_AUTH_GITHUB_TIMEOUT_SECONDS
默认值:"10"
GitHub API 调用的 HTTP 请求超时
示例 .env 文件：
# 使用 GitHub 提供程序
FASTMCP_SERVER_AUTH=fastmcp.server.auth.providers.github.GitHubProvider

# GitHub OAuth 凭据
FASTMCP_SERVER_AUTH_GITHUB_CLIENT_ID=Ov23liAbcDefGhiJkLmN
FASTMCP_SERVER_AUTH_GITHUB_CLIENT_SECRET=github_pat_...
FASTMCP_SERVER_AUTH_GITHUB_BASE_URL=https://your-server.com
FASTMCP_SERVER_AUTH_GITHUB_REQUIRED_SCOPES=user,repo

设置环境变量后，您的服务器代码简化为：
server.py
from fastmcp import FastMCP

# 身份验证从环境中自动配置
mcp = FastMCP(name="GitHub 安全应用")

@mcp.tool
async def list_repos() -> list[str]:
    """列出经过身份验证用户的仓库。"""
    # 您的工具实现在这里
    pass

Eunomia Authorization 🤝 FastMCP

Google OAuth 🤝 FastMCP

x

---

配置
先决条件
第 1 步：创建 Google OAuth 2.0 客户端 ID
第 2 步：FastMCP 配置
测试
运行服务器
使用客户端测试
生产环境配置
环境变量
提供程序选择
Google 特定配置
认证
Google OAuth 🤝 FastMCP

使用 Google OAuth 保护您的 FastMCP 服务器

版本
2.12.0
新增
本指南将向您展示如何使用 Google OAuth 保护您的 FastMCP 服务器。由于 Google 不支持动态客户端注册，此集成使用 OAuth 代理 模式来连接 Google 的传统 OAuth 与 MCP 的身份验证要求。

配置

先决条件
在开始之前，您需要：
一个具有创建 OAuth 2.0 客户端 ID 权限的 Google Cloud 账户
您的 FastMCP 服务器 URL（开发时可以是 localhost，例如 http://localhost:8000）

第 1 步：创建 Google OAuth 2.0 客户端 ID
在您的 Google Cloud 控制台中创建 OAuth 2.0 客户端 ID 以获取身份验证所需的凭据：
1

导航到 OAuth 同意屏

转到 Google Cloud 控制台并选择您的项目（或创建新项目）。
首先，通过导航到 API 和服务 → OAuth 同意屏 来配置 OAuth 同意屏。选择 “外部” 进行测试或为 G Suite 组织选择 “内部”。
2

创建 OAuth 2.0 客户端 ID

导航到 API 和服务 → 凭据 并点击 ”+ 创建凭据” → **“OAuth 客户端 ID”。
配置您的 OAuth 客户端：
应用程序类型：Web 应用程序
名称：选择描述性名称（例如，“FastMCP 服务器”）
授权的 JavaScript 源：添加您服务器的基本 URL（例如，http://localhost:8000）
授权的重定向 URI：添加您的服务器 URL + /auth/callback（例如，http://localhost:8000/auth/callback）
重定向 URI 必须完全匹配。默认路径是 /auth/callback，但您可以使用 redirect_path 参数进行自定义。对于本地开发，Google 允许不同端口的 http://localhost URL。对于生产环境，您必须使用 HTTPS。
如果您想使用自定义回调路径（例如，/auth/google/callback），请确保在 Google OAuth 客户端设置和配置 GoogleProvider 时的 redirect_path 参数中设置相同的路径。
3

保存您的凭据

创建客户端后，您将收到：
客户端 ID：以 .apps.googleusercontent.com 结尾的字符串
客户端密钥：以 GOCSPX- 开头的字符串
下载 JSON 凭据或安全复制这些值。
安全存储这些凭据。永远不要将它们提交到版本控制。在生产环境中使用环境变量或密钥管理器。

第 2 步：FastMCP 配置
使用 GoogleProvider 创建您的 FastMCP 服务器，它会自动处理 Google 的 OAuth 流程：
server.py
from fastmcp import FastMCP
from fastmcp.server.auth.providers.google import GoogleProvider

# GoogleProvider 处理 Google 的令牌格式和验证
auth_provider = GoogleProvider(
    client_id="123456789.apps.googleusercontent.com",  # 您的 Google OAuth 客户端 ID
    client_secret="GOCSPX-abc123...",                  # 您的 Google OAuth 客户端密钥
    base_url="http://localhost:8000",                  # 必须与您的 OAuth 配置匹配
    required_scopes=[                                  # Request user information
        "openid",
        "https://www.googleapis.com/auth/userinfo.email",
    ],
    # redirect_path="/auth/callback"                  # 默认值，如需可自定义
)

mcp = FastMCP(name="Google Secured App", auth=auth_provider)

# 添加受保护的工具来测试身份验证
@mcp.tool
async def get_user_info() -> dict:
    """返回经过身份验证的 Google 用户信息。"""
    from fastmcp.server.dependencies import get_access_token
    
    token = get_access_token()
    # GoogleProvider 在令牌声明中存储用户数据
    return {
        "google_id": token.claims.get("sub"),
        "email": token.claims.get("email"),
        "name": token.claims.get("name"),
        "picture": token.claims.get("picture"),
        "locale": token.claims.get("locale")
    }

测试

运行服务器
使用 HTTP 传输启动您的 FastMCP 服务器以启用 OAuth 流程：
fastmcp run server.py --transport http --port 8000

您的服务器现在正在运行并受到 Google OAuth 身份验证的保护。

使用客户端测试
创建一个与您的 Google 保护服务器进行身份验证的测试客户端：
test_client.py
from fastmcp import Client
import asyncio

async def main():
    # 客户端将自动处理 Google OAuth
    async with Client("http://localhost:8000/mcp/", auth="oauth") as client:
        # 首次连接将在浏览器中打开 Google 登录
        print("✓ 已通过 Google 身份验证！")
        
        # 测试受保护的工具
        result = await client.call_tool("get_user_info")
        print(f"Google 用户：{result['email']}")
        print(f"姓名：{result['name']}")

if __name__ == "__main__":
    asyncio.run(main())

当您首次运行客户端时：
您的浏览器将打开 Google 的授权页面
使用您的 Google 账户登录并授予请求的权限
授权后，您将被重定向回去
客户端接收令牌并可以发出经过身份验证的请求
客户端在本地缓存令牌，因此除非令牌过期或您显式清除缓存，否则您无需为后续运行重新进行身份验证。

生产环境配置
版本
2.13.0
新增
在生产部署中，为了在服务器重启后仍能保留令牌，请同时配置 jwt_signing_key 与 client_storage：
server.py
import os
from fastmcp import FastMCP
from fastmcp.server.auth.providers.google import GoogleProvider
from key_value.aio.stores.redis import RedisStore
from key_value.aio.wrappers.encryption import FernetEncryptionWrapper
from cryptography.fernet import Fernet

# 使用加密的持久化令牌存储进行生产配置
auth_provider = GoogleProvider(
    client_id="123456789.apps.googleusercontent.com",
    client_secret="GOCSPX-abc123...",
    base_url="https://your-production-domain.com",
    required_scopes=["openid", "https://www.googleapis.com/auth/userinfo.email"],

    # 生产环境令牌管理
    jwt_signing_key=os.environ["JWT_SIGNING_KEY"],
    client_storage=FernetEncryptionWrapper(
        key_value=RedisStore(
            host=os.environ["REDIS_HOST"],
            port=int(os.environ["REDIS_PORT"])
        ),
        fernet=Fernet(os.environ["STORAGE_ENCRYPTION_KEY"])
    )
)

mcp = FastMCP(name="Production Google App", auth=auth_provider)

jwt_signing_key 与 client_storage 需要配合使用，才能在服务器重启后保留令牌与客户端注册信息。请使用 FernetEncryptionWrapper 加密存储中的敏感 OAuth 令牌，否则将以明文保存。建议通过环境变量存放密钥，并使用 Redis 等持久化后端以支持分布式部署。
更多参数详情请参阅 OAuth 代理文档。

环境变量
版本
2.12.1
新增
对于生产部署，请使用环境变量而不是硬编码凭据。

提供程序选择
设置此环境变量允许自动使用 Google 提供程序，而无需在代码中显式实例化它。

FASTMCP_SERVER_AUTH
默认值:"未设置"
设置为 fastmcp.server.auth.providers.google.GoogleProvider 以使用 Google 身份验证。

Google 特定配置
这些环境变量为 Google 提供程序提供默认值，无论它是手动实例化的还是通过 FASTMCP_SERVER_AUTH 配置的。

FASTMCP_SERVER_AUTH_GOOGLE_CLIENT_ID
必填
您的 Google OAuth 2.0 客户端 ID（例如，123456789.apps.googleusercontent.com）

FASTMCP_SERVER_AUTH_GOOGLE_CLIENT_SECRET
必填
您的 Google OAuth 2.0 客户端密钥（例如，GOCSPX-abc123...）

FASTMCP_SERVER_AUTH_GOOGLE_BASE_URL
默认值:"http://localhost:8000"
OAuth 端点可访问的公共 URL（包含任何挂载路径）

FASTMCP_SERVER_AUTH_GOOGLE_ISSUER_URL
默认值:"使用 BASE_URL"
用于 OAuth 元数据的 Issuer URL（默认等于 BASE_URL）。当挂载在路径前缀下时，请设置为根级 URL 以避免 404 日志。详见 HTTP 部署指南。

FASTMCP_SERVER_AUTH_GOOGLE_REDIRECT_PATH
默认值:"/auth/callback"
在您的 Google OAuth 客户端中配置的重定向路径

FASTMCP_SERVER_AUTH_GOOGLE_REQUIRED_SCOPES
默认值:"[]"
必需的 Google 作用域的逗号、空格或 JSON 分隔列表（例如，"openid,https://www.googleapis.com/auth/userinfo.email" 或 ["openid", "https://www.googleapis.com/auth/userinfo.email"]）

FASTMCP_SERVER_AUTH_GOOGLE_TIMEOUT_SECONDS
默认值:"10"
Google API 调用的 HTTP 请求超时
示例 .env 文件：
# 使用 Google 提供程序
FASTMCP_SERVER_AUTH=fastmcp.server.auth.providers.google.GoogleProvider

# Google OAuth 凭据
FASTMCP_SERVER_AUTH_GOOGLE_CLIENT_ID=123456789.apps.googleusercontent.com
FASTMCP_SERVER_AUTH_GOOGLE_CLIENT_SECRET=GOCSPX-abc123...
FASTMCP_SERVER_AUTH_GOOGLE_BASE_URL=https://your-server.com
FASTMCP_SERVER_AUTH_GOOGLE_REQUIRED_SCOPES=openid,https://www.googleapis.com/auth/userinfo.email

设置环境变量后，您的服务器代码简化为：
server.py
from fastmcp import FastMCP

# 身份验证从环境中自动配置
mcp = FastMCP(name="Google 安全应用")

@mcp.tool
async def protected_tool(query: str) -> str:
    """需要 Google 身份验证才能访问的工具。"""
    # 您的工具实现在这里
    return f"处理经过身份验证的请求：{query}"

GitHub OAuth 🤝 FastMCP

Keycloak OAuth 🤝 FastMCP

x

---
