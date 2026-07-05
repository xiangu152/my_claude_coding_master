---
title: "OIDC 代理"
source: "https://fastmcp.wiki/zh/servers/auth/oidc-proxy"
version: "latest"
---

# OIDC 代理

> 原始文档来源：https://fastmcp.wiki/zh/servers/auth/oidc-proxy

---

实现
提供商设置要求
基本设置
配置参数
使用内置提供商
作用域配置
环境配置
身份认证
OIDC 代理

桥接 OIDC 提供商，与 MCP 的认证流程无缝协作。

版本
2.12.4
新增
OIDC 代理使 FastMCP 服务器能够与**不支持动态客户端注册（DCR）**的 OIDC 提供商进行认证。这包括像 Auth0、Google、Azure、AWS 等 OAuth 提供商。对于支持 DCR 的提供商（如 WorkOS AuthKit），请改用 RemoteAuthProvider。
OIDC 代理基于 OAuthProxy 构建，因此在底层具有所有相同的功能。

实现

提供商设置要求
在使用 OIDC 代理之前，您需要在您的 OAuth 提供商处注册您的应用程序：
注册您的应用程序在提供商的开发者控制台中（Auth0 Applications、Google Cloud Console、Azure Portal 等）
配置重定向 URI为您的 FastMCP 服务器 URL 加上您选择的回调路径：
默认：https://your-server.com/auth/callback
自定义：https://your-server.com/your/custom/path（如果您设置了 redirect_path）
开发环境：http://localhost:8000/auth/callback
获取您的凭据：客户端 ID 和客户端密钥
您在提供商处配置的重定向 URI 必须与您的 FastMCP 服务器的 URL 加上回调路径完全匹配。如果您在 OIDC 代理中自定义了 redirect_path，请相应地更新提供商的重定向 URI。

基本设置
以下是如何使用任何提供商实现 OIDC 代理：
from fastmcp import FastMCP
from fastmcp.server.auth.oidc_proxy import OIDCProxy

# 创建 OIDC 代理
auth = OIDCProxy(
    # 提供商的配置 URL
    config_url="https://provider.com/.well-known/openid-configuration",

    # 您注册的应用程序凭据
    client_id="your-client-id",
    client_secret="your-client-secret",

    # 您的 FastMCP 服务器的公共 URL
    base_url="https://your-server.com",

    # 可选：自定义回调路径（默认为 "/auth/callback"）
    # redirect_path="/custom/callback",
)

mcp = FastMCP(name="My Server", auth=auth)

配置参数
OIDCProxy 参数

config_url
str必填
您的 OAuth 提供商的 OIDC 配置 URL

client_id
str必填
从您注册的 OAuth 应用程序获取的客户端 ID

client_secret
str必填
从您注册的 OAuth 应用程序获取的客户端密钥

base_url
AnyHttpUrl | str必填
您的 FastMCP 服务器的公共 URL（例如 https://your-server.com）

strict
bool | None
配置验证的严格标志。当为 True 时，需要所有 OIDC 必填字段。

audience
str | None
需要它的 OIDC 提供商的受众参数（例如 Auth0）。这通常是您的 API 标识符。

timeout_seconds
int | None默认值:"10"
获取 OIDC 配置的 HTTP 请求超时秒数

algorithm
str | None
用于令牌验证的 JWT 算法（例如 “RS256”）。如果未指定，使用提供商的默认值。

required_scopes
list[str] | None
向提供商请求的 OAuth 作用域列表。这些会自动包含在授权请求中。

redirect_path
str默认值:"/auth/callback"
OAuth 回调的路径。必须与您的 OAuth 应用程序中配置的重定向 URI 匹配

allowed_client_redirect_uris
list[str] | None
MCP 客户端允许的重定向 URI 模式列表。模式支持通配符（例如 "http://localhost:*"、"https://*.example.com/*"）。
None（默认）：允许所有重定向 URI（为了 MCP/DCR 兼容性）
空列表 []：不允许任何重定向 URI
自定义列表：只允许匹配的模式
这些模式适用于 MCP 客户端回环重定向，而不是上游 OAuth 应用程序重定向 URI。

token_endpoint_auth_method
str | None
上游 OAuth 服务器的令牌端点认证方法。控制代理在与上游提供商交换授权代码和刷新令牌时如何进行认证。
"client_secret_basic"：在 Authorization 标头中发送凭据（最常见）
"client_secret_post"：在请求体中发送凭据（某些提供商要求）
"none"：无认证（对于公共客户端）
None（默认）：使用 authlib 的默认值（通常是 "client_secret_basic"）
如果您的提供商需要特定的认证方法且默认方法不起作用，请设置此参数。

jwt_signing_key
str | bytes | None
版本
2.13.0
新增
用于签名颁发给客户端的 FastMCP JWT 令牌的密钥。接受任何字符串或字节 - 将使用 HKDF 派生为适当的 32 字节加密密钥。
默认行为（None）：
Mac/Windows：通过系统密钥环自动管理。密钥只生成一次并持久化，在零配置的情况下能在服务器重启后继续存在。密钥自动从服务器属性派生，因此这种方法虽然方便，但仅适用于开发和本地测试。对于生产环境，您必须提供显式的密钥。
Linux：临时（启动时随机盐）。令牌在服务器重启时失效，触发客户端重新认证。
对于生产环境： 提供显式的密钥（例如从环境变量获取）以使用固定密钥而不是自动生成的密钥。

client_storage
AsyncKeyValue | None
版本
2.13.0
新增
用于持久化 OAuth 客户端注册和上游令牌的存储后端。
默认行为：
Mac/Windows：在您平台的数据目录中的加密 DiskStore（从 platformdirs 派生）
Linux：MemoryStore（临时 - 重启时客户端丢失）
默认在 Mac/Windows 上，客户端会自动持久化到加密磁盘存储，只要文件系统保持可访问，它们就能在服务器重启后继续存在。这意味着 MCP 客户端只需要注册一次就可以无缝重新连接。在密钥环不可用的 Linux 上，使用临时存储以匹配临时密钥策略。
对于多服务器或云部署的生产环境，请使用网络可访问的存储后端而不是本地磁盘存储。**将您的存储包装在 FernetEncryptionWrapper 中以加密静态的敏感 OAuth 令牌。**有关可用选项，请参阅 存储后端。
使用内存存储进行测试（未加密）：
from key_value.aio.stores.memory import MemoryStore

# 使用内存存储进行测试（重启时客户端丢失）
auth = OIDCProxy(..., client_storage=MemoryStore())

使用加密 Redis 存储的生产环境：
from key_value.aio.stores.redis import RedisStore
from key_value.aio.wrappers.encryption import FernetEncryptionWrapper
from cryptography.fernet import Fernet
import os

auth = OIDCProxy(
    ...,
    jwt_signing_key=os.environ["JWT_SIGNING_KEY"],
    client_storage=FernetEncryptionWrapper(
        key_value=RedisStore(host="redis.example.com", port=6379),
        fernet=Fernet(os.environ["STORAGE_ENCRYPTION_KEY"])
    )
)

使用内置提供商
FastMCP 包含常见服务的预配置 OIDC 提供商：
from fastmcp.server.auth.providers.auth0 import Auth0Provider

auth = Auth0Provider(
    config_url="https://.../.well-known/openid-configuration",
    client_id="your-auth0-client-id",
    client_secret="your-auth0-client-secret",
    audience="https://...",
    base_url="https://localhost:8000"
)

mcp = FastMCP(name="My Server", auth=auth)

目前可用的提供商包括 Auth0Provider。

作用域配置
OAuth 作用域通过 required_scopes 配置，以自动请求您的应用程序所需的权限。
代理创建的动态客户端将自动在其授权请求中包含这些作用域。

环境配置
版本
2.13.0
新增
对于生产部署，通过环境变量配置 OIDC 代理，而不是硬编码凭据：
# 指定提供商实现
export FASTMCP_SERVER_AUTH=fastmcp.server.auth.providers.auth0.Auth0Provider

# 提供商特定的凭据
export FASTMCP_SERVER_AUTH_AUTH0_CONFIG_URL=https://.../.well-known/openid-configuration
export FASTMCP_SERVER_AUTH_AUTH0_CLIENT_ID=tv2ObNgaZAWWhhycr7Bz1LU2mxlnsmsB
export FASTMCP_SERVER_AUTH_AUTH0_CLIENT_SECRET=vPYqbjemq...
export FASTMCP_SERVER_AUTH_AUTH0_AUDIENCE=https://...
export FASTMCP_SERVER_AUTH_AUTH0_BASE_URL=https://localhost:8000

使用环境配置，您的服务器代码简化为：
from fastmcp import FastMCP

# 认证从环境自动配置
mcp = FastMCP(name="My Server")

@mcp.tool
def protected_tool(data: str) -> str:
    """此工具现在受 OAuth 保护。"""
    return f"已处理：{data}"

if __name__ == "__main__":
    mcp.run(transport="http", port=8000)

OAuth 代理

完整 OAuth 服务器

x

