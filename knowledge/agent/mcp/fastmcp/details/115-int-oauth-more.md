---
title: "集成: 更多 OAuth 提供商 (Keycloak, OCI, Permit, PropelAuth, Scalekit, Supabase, WorkOS)"
source: "https://fastmcp.wiki/zh/integrations/keycloak"
version: "latest"
---

# 集成: 更多 OAuth 提供商 (Keycloak, OCI, Permit, PropelAuth, Scalekit, Supabase, WorkOS)

> 原始文档来源：https://fastmcp.wiki/zh/integrations/keycloak (FastMCP 集成文档)

---

配置
前置条件
FastMCP 配置
本地开发
测试
运行服务端
使用客户端测试
特性
JWT 令牌验证
用户 Claims
高级配置
自定义令牌验证器
认证
Keycloak OAuth 🤝 FastMCP

使用 Keycloak OAuth 保护你的 FastMCP 服务端

版本
3.2.4
新增
本指南展示如何使用 Keycloak OAuth 保护你的 FastMCP 服务端。该集成使用带 Dynamic Client Registration（DCR）的 Remote OAuth 模式，由 Keycloak 处理用户登录，而你的 FastMCP 服务端负责验证令牌。
需要 Keycloak 26.6.0 或更高版本。 更早版本存在与 MCP 客户端不兼容的 DCR 问题（PR #45309），该问题已在 26.6.0 中修复。

配置

前置条件
开始之前，你需要：
一个正在运行的 Keycloak 实例（例如 http://localhost:8080）
一个已启用 Dynamic Client Registration 的 Keycloak realm，并配置允许你的服务端 URL（例如 http://localhost:8000/*）的可信主机策略
你的 FastMCP 服务端公共 URL（例如 http://localhost:8000）

FastMCP 配置
创建 FastMCP 服务端，并使用 KeycloakAuthProvider 处理 OAuth：
server.py
import os

from fastmcp import FastMCP
from fastmcp.server.auth.providers.keycloak import KeycloakAuthProvider
from fastmcp.server.dependencies import get_access_token

auth = KeycloakAuthProvider(
    realm_url=os.getenv("KEYCLOAK_REALM_URL") or "http://localhost:8080/realms/myrealm",
    base_url="http://localhost:8000",
    # audience="http://localhost:8000",  # 生产环境推荐设置
)

mcp = FastMCP("Keycloak Example Server", auth=auth)

@mcp.tool
async def get_access_token_claims() -> dict:
    """获取已认证用户的访问令牌 claims。"""
    token = get_access_token()
    return {
        "sub": token.claims.get("sub"),
        "scope": token.claims.get("scope"),
        "azp": token.claims.get("azp"),
    }

生产安全性：生产环境中请始终配置 audience 参数。没有它时，服务端会接受为任何 audience 签发的令牌。请配置 Keycloak audience mapper，并将 audience 设置为服务端的基础 URL，以确保令牌确实是发给你的服务端的。

本地开发
为保持身份认证集成精简，并尽可能降低相关维护负担，本地基础设施工具有意没有放入 FastMCP 核心库。不过，Keycloak 是本地开发和测试中常用的身份提供商，因此配套项目 fastmcp-keycloak-local 提供了一套专门兼容 FastMCP 的设置蓝图。
它提供了在本地使用 Keycloak OAuth 开发和测试 FastMCP 服务端所需的一切：基于 Docker 的 Keycloak 设置，带预配置的 fastmcp realm（已启用 Dynamic Client Registration，并包含测试用户）、跨平台启动脚本，以及面向 MCP Inspector、Claude Desktop 和 Claude Code CLI 的集成指南。

测试

运行服务端
fastmcp run server.py --transport http --port 8000

使用客户端测试
client.py
import asyncio
from fastmcp import Client

async def main():
    async with Client("http://localhost:8000/mcp", auth="oauth") as client:
        print("✓ Authenticated with Keycloak!")
        result = await client.call_tool("get_access_token_claims")
        print(f"sub: {result.data.get('sub', 'N/A')}")

asyncio.run(main())

首次运行时，浏览器会打开 Keycloak 的授权页面。登录后，客户端会收到令牌，并将其缓存以供后续运行使用。

特性

JWT 令牌验证
签名验证：根据 Keycloak 的 JWKS 端点验证令牌
过期检查：自动拒绝已过期令牌
签发者验证：确保令牌来自你的特定 Keycloak realm
Scope 强制检查：验证是否存在所需 OAuth scope
Audience 验证：可选验证令牌是否面向你的服务端（配置 audience）

用户 Claims
从 Keycloak JWT 令牌访问用户信息：
from fastmcp.server.dependencies import get_access_token

@mcp.tool
async def admin_only_tool() -> str:
    """仅管理员用户可用的工具。"""
    token = get_access_token()
    roles = token.claims.get("realm_access", {}).get("roles", [])
    if "admin" not in roles:
        raise ValueError("This tool requires admin access")
    return "Admin access granted!"

高级配置

自定义令牌验证器
from fastmcp.server.auth.providers.jwt import JWTVerifier
from fastmcp.server.auth.providers.keycloak import KeycloakAuthProvider

custom_verifier = JWTVerifier(
    jwks_uri="http://localhost:8080/realms/myrealm/protocol/openid-connect/certs",
    issuer="http://localhost:8080/realms/myrealm",
    audience="my-resource-server",
    required_scopes=["api:read", "api:write"],
)

auth = KeycloakAuthProvider(
    realm_url="http://localhost:8080/realms/myrealm",
    base_url="http://localhost:8000",
    token_verifier=custom_verifier,
)

Google OAuth 🤝 FastMCP

OCI IAM OAuth 🤝 FastMCP

x

---

配置
前置条件
第 1 步：确保已为 JWK URL 启用客户端访问
第 2 步：为 MCP 服务端认证创建 OAuth 客户端
第 3 步：令牌交换设置（仅当 MCP 服务端需要访问 OCI 控制平面时）
运行 MCP 服务端
生产配置
认证
OCI IAM OAuth 🤝 FastMCP

使用 OCI IAM OAuth 保护你的 FastMCP 服务端

版本
2.13.0
新增
本指南展示如何使用 OCI IAM OAuth 保护你的 FastMCP 服务端。由于 OCI IAM 不支持动态客户端注册，此集成使用 OIDC 代理 模式，将 OCI 的传统 OAuth 与 MCP 的认证要求连接起来。

配置

前置条件
一个 OCI 云账号，并且有权限在 Identity Domain 中创建 Integrated Application。
你的 FastMCP 服务端 URL（开发环境中通常是 http://localhost:8000；生产环境中可能是 https://mcp.yourdomain.com）。

第 1 步：确保已为 JWK URL 启用客户端访问
1

进入 OCI IAM Domain Settings

登录 OCI 控制台（OCI 商业云为 https://cloud.oracle.com）。 从 “Identity & Security” 菜单打开 Domains 页面。 在 Domains 列表页中，选择用于 MCP 认证的 domain。 打开 Settings 标签页。 点击 “Edit Domain Settings” 按钮。
2

更新 Domain 设置

如截图所示，启用 “Configure client access” 复选框。

第 2 步：为 MCP 服务端认证创建 OAuth 客户端
按照以下步骤创建 OAuth 客户端。
1

进入 OCI IAM Integrated Applications

登录 OCI 控制台（OCI 商业云为 https://cloud.oracle.com）。 从 “Identity & Security” 菜单打开 Domains 页面。 在 Domains 列表页中，选择要在其中创建 MCP 服务端 OAuth 客户端的 domain。如果需要帮助查找 domain 列表页，请参见 Listing Identity Domains。 在详情页中，选择 Integrated applications。页面会显示该 domain 中的应用列表。
2

添加 Integrated Application

选择 Add application。 在 Add application 窗口中，选择 Confidential Application。 选择 Launch workflow。 在 Add application details 页面中，按下图输入名称和描述。
3

更新 Integrated Application 的 OAuth 配置

Integrated Application 创建完成后，点击 “OAuth configuration” 标签页。 点击 “Edit OAuth configuration” 按钮。 通过选择 “Configure this application as a client now” 单选按钮，将该应用配置为 OAuth 客户端。 选择 “Authorization code” grant type。如果计划使用同一个 OAuth 客户端应用进行令牌交换，也请选择 “Client credentials” grant type。在示例中，我们会使用同一个客户端。 对于 Authorization grant type，选择重定向 URL。大多数情况下，它会是 MCP 服务端 URL 后接 “/oauth/callback”。
4

激活 Integrated Application

点击 “Submit” 按钮，更新客户端应用的 OAuth 配置。 注意：你不需要做任何特殊配置来让 OAuth 客户端支持 PKCE。 确保激活客户端应用。 记下该应用的 client ID 和 client secret。配置代码中的 OCIProvider 时会用到这些值。
以上就是针对 OCI IAM 实现 MCP 服务端认证所需的全部内容。不过，你可能希望使用已认证用户令牌来调用 OCI 控制平面 API，并把身份传播到 OCI 控制平面，而不是使用服务用户账号。此时需要实现令牌交换。

第 3 步：令牌交换设置（仅当 MCP 服务端需要访问 OCI 控制平面时）
令牌交换可帮助你把已登录用户的 OCI IAM 令牌交换为 OCI 控制平面会话令牌，也称为 UPST（User Principal Session Token）。如需了解令牌交换，请参阅我的 Workload Identity Federation 博客。
对于令牌交换，我们需要配置信任的身份传播。上面的博客介绍了如何使用 REST API 建立信任。不过，你也可以使用 OCI CLI。使用下面的 CLI 命令前，请确保已经创建了令牌交换 OAuth 客户端。大多数情况下，可以使用上面创建的同一个 OAuth 客户端。请将下面 CLI 命令中的 <IAM_GUID> 和 <CLIENT_ID> 替换为你的实际值。
oci identity-domains identity-propagation-trust create \
--schemas '["urn:ietf:params:scim:schemas:oracle:idcs:IdentityPropagationTrust"]' \
--public-key-endpoint "https://<IAM_GUID>.identity.oraclecloud.com/admin/v1/SigningCert/jwk" \
--name "For Token Exchange" --type "JWT" \
--issuer "https://identity.oraclecloud.com/" --active true \
--endpoint "https://<IAM_GUID>.identity.oraclecloud.com" \
--subject-claim-name "sub" --allow-impersonation false \
--subject-mapping-attribute "username" \
--subject-type "User" --client-claim-name "iss" \
--client-claim-values '["https://identity.oraclecloud.com/"]' \
--oauth-clients '["<CLIENT_ID>"]'

要将访问令牌交换为 OCI 令牌并创建 signer 对象，需要在 MCP 服务端中添加以下代码。随后可以使用该 signer 对象创建任意 OCI 控制平面客户端。

from fastmcp.server.dependencies import get_access_token
from fastmcp.utilities.logging import get_logger
from oci.auth.signers import TokenExchangeSigner
import os

logger = get_logger(__name__)

# 从环境加载配置
OCI_IAM_GUID = os.environ.get("OCI_IAM_GUID")
OCI_CLIENT_ID = os.environ.get("OCI_CLIENT_ID")
OCI_CLIENT_SECRET = os.environ.get("OCI_CLIENT_SECRET")

_global_token_cache = {} # OCI 会话令牌 signer 的内存缓存
    
def get_oci_signer() -> TokenExchangeSigner:

    authntoken = get_access_token()
    tokenID = authntoken.claims.get("jti")
    token = authntoken.token
    
    # 检查内存缓存中是否已存在该 token ID 对应的 signer
    cached_signer = _global_token_cache.get(tokenID)
    logger.debug(f"Global cached signer: {cached_signer}")
    if cached_signer:
        logger.debug(f"Using globally cached signer for token ID: {tokenID}")
        return cached_signer

    # 如果尚未为该 token 创建 signer，则创建新的 OCI signer 对象
    logger.debug(f"Creating new signer for token ID: {tokenID}")
    signer = TokenExchangeSigner(
        jwt_or_func=token,
        oci_domain_id=OCI_IAM_GUID.split(".")[0] if OCI_IAM_GUID else "",
        client_id=OCI_CLIENT_ID,
        client_secret=OCI_CLIENT_SECRET,
    )
    logger.debug(f"Signer {signer} created for token ID: {tokenID}")
        
    # 将 signer 对象缓存到内存缓存中
    _global_token_cache[tokenID] = signer
    logger.debug(f"Signer cached for token ID: {tokenID}")

    return signer

运行 MCP 服务端
设置完成后，运行以下命令启动 MCP 服务端。
fastmcp run server.py:mcp --transport http --port 8000

运行 MCP 客户端：
python3 client.py

MCP 客户端示例如下。
client.py
from fastmcp import Client
import asyncio

async def main():
    # 客户端会自动处理 OCI OAuth 流程
    async with Client("http://localhost:8000/mcp/", auth="oauth") as client:
        # 首次连接会在浏览器中打开 OCI 登录
        print("✓ Authenticated with OCI IAM")

        tools = await client.list_tools()
        print(f"🔧 Available tools ({len(tools)}):")
        for tool in tools:
            print(f"   - {tool.name}: {tool.description}")

if __name__ == "__main__":
    asyncio.run(main())

首次运行客户端时：
浏览器会打开 OCI IAM 的登录页面
使用你的 OCI 账号登录，并授予请求的同意
授权后，你会被重定向回重定向路径
客户端接收令牌，并可以发起已认证请求

生产配置
版本
2.13.0
新增
对于需要在服务端重启后保持令牌管理状态的生产部署，请配置 jwt_signing_key 和 client_storage：
server.py

import os
from fastmcp import FastMCP
from fastmcp.server.auth.providers.oci import OCIProvider

from key_value.aio.stores.redis import RedisStore
from key_value.aio.wrappers.encryption import FernetEncryptionWrapper
from cryptography.fernet import Fernet

# 从环境加载配置
# 使用加密持久化令牌存储的生产设置
auth_provider = OCIProvider(
    config_url=os.environ.get("OCI_CONFIG_URL"),
    client_id=os.environ.get("OCI_CLIENT_ID"),
    client_secret=os.environ.get("OCI_CLIENT_SECRET"),
    base_url=os.environ.get("BASE_URL", "https://your-production-domain.com"),

    # 生产令牌管理
    jwt_signing_key=os.environ["JWT_SIGNING_KEY"],
    client_storage=FernetEncryptionWrapper(
        key_value=RedisStore(
            host=os.environ["REDIS_HOST"],
            port=int(os.environ["REDIS_PORT"])
        ),
        fernet=Fernet(os.environ["STORAGE_ENCRYPTION_KEY"])
    )
)

mcp = FastMCP(name="Production OCI App", auth=auth_provider)

参数（jwt_signing_key 和 client_storage）共同确保令牌和客户端注册在服务端重启后仍然保留。请用 FernetEncryptionWrapper 包装你的存储，以加密静态敏感 OAuth 令牌；否则，令牌会以明文存储。请把 secret 存储在环境变量中，并在分布式部署中使用 Redis 这类持久化存储后端。
这些参数的完整说明请参见 OAuth 代理文档。
客户端会在本地缓存令牌，因此后续运行时通常不需要重新认证，除非令牌过期或你显式清除了缓存。
Keycloak OAuth 🤝 FastMCP

Permit.io 身份认证 🤝 FastMCP

x

---

工作原理
策略映射
列表操作
执行操作
为您的服务器添加授权
先决条件
运行 Permit.io PDP
创建带有授权的服务器
配置访问策略
示例策略配置
身份管理
JWT 身份验证示例
带有工具参数的 ABAC 策略
示例：条件访问
运行服务器
高级配置
环境变量
自定义中间件配置
示例：完整的 JWT 身份验证服务器
认证
Permit.io 身份认证 🤝 FastMCP

使用 Permit.io 为您的 FastMCP 服务器添加细粒度授权

使用 Permit.io 授权中间件通过一行代码添加为您的 FastMCP 服务器添加基于策略的授权。
控制 MCP 客户端可以在您的服务器上查看和执行的工具、资源和提示。使用 Permit.io 强大的 RBAC、ABAC 和 REBAC 功能定义动态策略，并获得所有访问尝试和违规行为的全面审计日志。

工作原理
利用 FastMCP 的中间件，Permit.io 中间件拦截所有到您服务器的 MCP 请求，并自动将 MCP 方法映射到针对您的 Permit.io 策略的授权检查；涵盖服务器方法和工具执行。

策略映射
中间件自动将 MCP 方法映射到 Permit.io 资源和操作：
MCP 服务器方法（例如，tools/list、resources/read）：
资源：{server_name}_{component}（例如，myserver_tools）
操作：方法动词（例如，list、read）
工具执行（方法 tools/call）：
资源：{server_name}（例如，myserver）
操作：工具名称（例如，greet）
示例：在 Permit.io 中，‘Admin’ 角色被授予中间件映射的资源和操作权限。例如，‘greet’、‘greet-jwt’ 和 ‘login’ 是 ‘mcp_server’ 资源上的操作，而 ‘list’ 是 ‘mcp_server_tools’ 资源上的操作。
注意： 不要忘记在 Permit.io 目录中为对您的 MCP 服务器进行身份验证的用户（例如 JWT 中的用户）分配相关角色（例如，Admin、User）。如果没有正确的角色分配，用户将无法访问您在策略中配置的资源和操作。
示例：在 Permit.io 目录中，‘client’ 和 ‘admin’ 用户都被分配了 ‘Admin’ 角色，授予他们您在策略映射中定义的权限。
有关详细的策略映射示例和配置，请参阅详细策略映射。

列表操作
中间件作为列表操作（tools/list、resources/list、prompts/list）的过滤器，对客户端隐藏未被定义策略授权的组件。
Permit.io PDP
FastMCP 服务器
Permit.io 中间件
MCP 客户端
Permit.io PDP
FastMCP 服务器
Permit.io 中间件
MCP 客户端
MCP 列表请求（例如，tools/list）
MCP 列表请求
MCP 列表响应
授权检查
授权决策
过滤后的 MCP 列表响应

执行操作
中间件作为执行操作（tools/call、resources/read、prompts/get）的强制执行点，阻止未被定义策略授权的操作。
Permit.io PDP
FastMCP 服务器
Permit.io 中间件
MCP 客户端
Permit.io PDP
FastMCP 服务器
Permit.io 中间件
MCP 客户端
MCP 执行请求（例如，tools/call）
授权检查
授权决策
MCP 未授权错误（如果被拒绝）
MCP 执行请求（如果允许）
MCP 执行响应（如果允许）
MCP 执行响应（如果允许）

为您的服务器添加授权
Permit.io 是一个云原生授权服务。您需要 Permit.io 账户和正在运行的策略决策点（PDP）才能使中间件正常工作。您可以使用 Docker 在本地运行 PDP，或使用 Permit.io 的云 PDP。

先决条件
Permit.io 账户：在 permit.io 注册
PDP 设置：在本地运行 Permit.io PDP 或使用云 PDP（仅 RBAC）
API 密钥：从仪表板获取您的 Permit.io API 密钥

运行 Permit.io PDP
使用 Docker 在本地运行 PDP：
docker run -p 7766:7766 permitio/pdp:latest

或使用云 PDP URL：https://cloudpdp.api.permit.io

创建带有授权的服务器
首先，安装 permit-fastmcp 包：
# 使用 UV（推荐）
uv add permit-fastmcp

# 使用 pip
pip install permit-fastmcp

然后创建一个 FastMCP 服务器并添加 Permit.io 中间件：
server.py
from fastmcp import FastMCP
from permit_fastmcp.middleware.middleware import PermitMcpMiddleware

mcp = FastMCP("安全的 FastMCP 服务器 🔒")

@mcp.tool
def greet(name: str) -> str:
    """按名称问候用户"""
    return f"您好，{name}！"

@mcp.tool
def add(a: int, b: int) -> int:
    """两个数相加"""
    return a + b

# 添加 Permit.io 授权中间件
mcp.add_middleware(PermitMcpMiddleware(
    permit_pdp_url="http://localhost:7766",
    permit_api_key="your-permit-api-key"
))

if __name__ == "__main__":
    mcp.run(transport="http")

配置访问策略
在 Permit.io 仪表板中创建您的授权策略：
创建资源：定义像 mcp_server 和 mcp_server_tools 这样的资源
定义操作：添加像 greet、add、list、read 这样的操作
创建角色：定义像 Admin、User、Guest 这样的角色
分配权限：授予角色对特定资源和操作的访问权
分配用户：在 Permit.io 目录中为用户分配角色
有关逐步设置说明和故障排除，请参阅入门指南和常见问题。

示例策略配置
策略在 Permit.io 仪表板中定义，但您也可以使用 Permit.io Terraform 提供程序在代码中定义策略。
# 资源
resource "permitio_resource" "mcp_server" {
  name = "mcp_server"
  key  = "mcp_server"
  
  actions = {
    "greet" = { name = "greet" }
    "add"   = { name = "add" }
  }
}

resource "permitio_resource" "mcp_server_tools" {
  name = "mcp_server_tools"
  key  = "mcp_server_tools"
  
  actions = {
    "list" = { name = "list" }
  }
}

# 角色
resource "permitio_role" "Admin" {
  key         = "Admin"
  name        = "Admin"
  permissions = [
    "mcp_server:greet",
    "mcp_server:add", 
    "mcp_server_tools:list"
  ]
}

您也可以使用 Permit.io CLI、API 或 SDK 来管理策略，以及直接用 REGO（Open Policy Agent 的策略语言）编写策略。
有关包括 ABAC 和 RBAC 配置在内的完整策略示例，请参阅示例策略。

身份管理
中间件支持多种身份提取模式：
固定身份：对所有请求使用固定身份
基于请求头：从 HTTP 请求头提取身份
基于 JWT：提取和验证 JWT 令牌
基于源：使用 MCP 上下文源字段
有关详细的身份模式配置和环境变量，请参阅身份模式和环境变量。

JWT 身份验证示例
import os

# 配置 JWT 身份提取
os.environ["PERMIT_MCP_IDENTITY_MODE"] = "jwt"
os.environ["PERMIT_MCP_IDENTITY_JWT_SECRET"] = "your-jwt-secret"

mcp.add_middleware(PermitMcpMiddleware(
    permit_pdp_url="http://localhost:7766",
    permit_api_key="your-permit-api-key"
))

带有工具参数的 ABAC 策略
中间件支持基于属性的访问控制（ABAC）策略，可以将工具参数作为属性进行评估。工具参数会自动展平为单独的属性（例如，arg_name、arg_number），用于细粒度的策略条件。
示例：创建带有 resource.arg_number greater-than 10 等条件的动态资源，仅当数字参数超过 10 时才允许 conditional-greet 工具。

示例：条件访问
创建一个带有 resource.arg_number greater-than 10 等条件的动态资源，仅当数字参数超过 10 时才允许 conditional-greet 工具。
@mcp.tool
def conditional_greet(name: str, number: int) -> str:
    """仅当数字 > 10 时问候用户"""
    return f"您好，{name}！您的数字是 {number}"

示例：Admin 角色被授予在”Big-greets”动态资源上的”conditional-greet”操作访问权限，而其他工具如”greet”、“greet-jwt”和”login”则在基础”mcp_server”资源上被授予权限。
有关全面的 ABAC 配置和高级策略示例，请参阅带有工具参数的 ABAC 策略。

运行服务器
正常启动您的 FastMCP 服务器：
python server.py

中间件现在将拦截所有 MCP 请求并根据您的 Permit.io 策略进行检查。请求包括通过配置的身份模式进行用户识别，以及 MCP 方法到授权资源和操作的自动映射。

高级配置

环境变量
使用环境变量配置中间件：
# Permit.io 配置
export PERMIT_MCP_PERMIT_PDP_URL="http://localhost:7766"
export PERMIT_MCP_PERMIT_API_KEY="your-api-key"

# 身份配置
export PERMIT_MCP_IDENTITY_MODE="jwt"
export PERMIT_MCP_IDENTITY_JWT_SECRET="your-jwt-secret"

# 方法配置
export PERMIT_MCP_KNOWN_METHODS='["tools/list","tools/call"]'
export PERMIT_MCP_BYPASSED_METHODS='["initialize","ping"]'

# 日志配置
export PERMIT_MCP_ENABLE_AUDIT_LOGGING="true"

有关所有配置选项和环境变量的完整列表，请参阅配置参考。

自定义中间件配置
from permit_fastmcp.middleware.middleware import PermitMcpMiddleware

middleware = PermitMcpMiddleware(
    permit_pdp_url="http://localhost:7766",
    permit_api_key="your-api-key",
    enable_audit_logging=True,
    bypass_methods=["initialize", "ping", "health/*"]
)

mcp.add_middleware(middleware)

有关高级配置选项和自定义中间件扩展，请参阅高级配置。

示例：完整的 JWT 身份验证服务器
请参阅示例服务器了解基于 JWT 身份验证的完整实现。有关其他示例和使用模式，请参阅示例服务器：
from fastmcp import FastMCP, Context
from permit_fastmcp.middleware.middleware import PermitMcpMiddleware
import jwt
import datetime

# 配置 JWT 身份提取
os.environ["PERMIT_MCP_IDENTITY_MODE"] = "jwt"
os.environ["PERMIT_MCP_IDENTITY_JWT_SECRET"] = "mysecretkey"

mcp = FastMCP("我的 MCP 服务器")

@mcp.tool
def login(username: str, password: str) -> str:
    """登录以获取 JWT 令牌"""
    if username == "admin" and password == "password":
        token = jwt.encode(
            {"sub": username, "exp": datetime.datetime.utcnow() + datetime.timedelta(hours=1)},
            "mysecretkey",
            algorithm="HS256"
        )
        return f"Bearer {token}"
    raise Exception("无效凭据")

@mcp.tool
def greet_jwt(ctx: Context) -> str:
    """通过从 JWT 中提取用户名来问候用户"""
    # JWT 提取由中间件处理
    return "您好，已认证用户！"

mcp.add_middleware(PermitMcpMiddleware(
    permit_pdp_url="http://localhost:7766",
    permit_api_key="your-permit-api-key"
))

if __name__ == "__main__":
    mcp.run(transport="http")

有关详细的策略配置、自定义身份验证和高级部署模式，请访问 Permit.io FastMCP 中间件 仓库。有关常见问题的故障排除，请参阅故障排除。
OCI IAM OAuth 🤝 FastMCP

PropelAuth 🤝 FastMCP

x

---

配置
前置条件
第 1 步：配置 PropelAuth
第 2 步：环境设置
第 3 步：FastMCP 配置
测试
访问用户信息
高级配置
认证
PropelAuth 🤝 FastMCP

使用 PropelAuth 保护你的 FastMCP 服务端

版本
3.1.0
新增
本指南展示如何使用 PropelAuth 保护你的 FastMCP 服务端。PropelAuth 是完整的认证和用户管理解决方案。此集成使用 远程 OAuth 模式：PropelAuth 负责用户登录和同意管理，你的 FastMCP 服务端负责验证令牌。

配置

前置条件
开始之前，你需要：
一个 PropelAuth 账号
你的 FastMCP 服务端基础 URL（开发时可以是 localhost，例如 http://localhost:8000）

第 1 步：配置 PropelAuth
1

启用 MCP 认证

在 PropelAuth 控制台中进入 MCP 部分，点击 Enable MCP，然后选择要启用的环境（Test、Staging、Prod）。
2

配置允许的 MCP 客户端

在 MCP > Allowed MCP Clients 下，为每个要允许的 MCP 客户端添加重定向 URI。PropelAuth 为 Claude、Cursor 和 ChatGPT 等常见客户端提供了模板。
3

配置作用域

在 MCP > Scopes 下，定义 MCP 客户端可用的权限（例如 read:user_data）。
4

选择用户创建 OAuth 客户端的方式

在 MCP > Settings > How Do Users Create OAuth Clients? 下，你可以选择性启用：
Dynamic Client Registration：客户端通过 DCR 协议自动自注册
Manually via Hosted Pages：PropelAuth 创建一个 UI，供你的用户注册 OAuth 客户端
你可以两者都不启用，也可以启用其中一个或两个。如果两者都不启用，则需要你自行管理 OAuth 客户端创建。
5

生成内省凭据

前往 MCP > Request Validation 并点击 Create Credentials。记下 Client ID 和 Client Secret，验证令牌时会用到它们。
6

记下你的 Auth URL

在控制台的 Backend Integration 部分找到你的 Auth URL（例如 https://auth.yourdomain.com）。
更多细节请参见 PropelAuth MCP 文档。

第 2 步：环境设置
创建一个 .env 文件，写入你的 PropelAuth 配置：
PROPELAUTH_AUTH_URL=https://auth.yourdomain.com          # 来自 Backend Integration 页面
PROPELAUTH_INTROSPECTION_CLIENT_ID=your-client-id        # 来自 MCP > Request Validation
PROPELAUTH_INTROSPECTION_CLIENT_SECRET=your-client-secret # 来自 MCP > Request Validation
SERVER_URL=http://localhost:8000                          # 你的服务端基础 URL

第 3 步：FastMCP 配置
创建你的 FastMCP 服务端文件，并使用 PropelAuthProvider 自动处理所有 OAuth 集成：
server.py
import os
from fastmcp import FastMCP
from fastmcp.server.auth.providers.propelauth import PropelAuthProvider

auth_provider = PropelAuthProvider(
    auth_url=os.environ["PROPELAUTH_AUTH_URL"],
    introspection_client_id=os.environ["PROPELAUTH_INTROSPECTION_CLIENT_ID"],
    introspection_client_secret=os.environ["PROPELAUTH_INTROSPECTION_CLIENT_SECRET"],
    base_url=os.environ["SERVER_URL"],
    required_scopes=["read:user_data"],                          # 可选的作用域强制检查
)

mcp = FastMCP(name="My PropelAuth Protected Server", auth=auth_provider)

测试
加载 .env 后，启动服务端：
fastmcp run server.py --transport http --port 8000

然后使用 FastMCP 客户端验证认证是否正常工作：
from fastmcp import Client
import asyncio

async def main():
    async with Client("http://localhost:8000/mcp", auth="oauth") as client:
        assert await client.ping()

if __name__ == "__main__":
    asyncio.run(main())

访问用户信息
你可以在工具中使用 get_access_token() 来识别已认证用户：
server.py
import os
from fastmcp import FastMCP
from fastmcp.server.auth.providers.propelauth import PropelAuthProvider
from fastmcp.server.dependencies import get_access_token

auth = PropelAuthProvider(
    auth_url=os.environ["PROPELAUTH_AUTH_URL"],
    introspection_client_id=os.environ["PROPELAUTH_INTROSPECTION_CLIENT_ID"],
    introspection_client_secret=os.environ["PROPELAUTH_INTROSPECTION_CLIENT_SECRET"],
    base_url=os.environ["SERVER_URL"],
    required_scopes=["read:user_data"],
)

mcp = FastMCP(name="My PropelAuth Protected Server", auth=auth)

@mcp.tool
def whoami() -> dict:
    """返回已认证用户的 ID。"""
    token = get_access_token()
    if token is None:
        return {"error": "未认证"}
    user_id = token.claims.get("sub")
    return {"user_id": user_id}

高级配置
PropelAuthProvider 支持对令牌内省行为进行可选覆盖，包括缓存和请求超时：
server.py
import os
from fastmcp import FastMCP
from fastmcp.server.auth.providers.propelauth import PropelAuthProvider

auth = PropelAuthProvider(
    auth_url=os.environ["PROPELAUTH_AUTH_URL"],
    introspection_client_id=os.environ["PROPELAUTH_INTROSPECTION_CLIENT_ID"],
    introspection_client_secret=os.environ["PROPELAUTH_INTROSPECTION_CLIENT_SECRET"],
    base_url=os.environ.get("BASE_URL", "https://your-server.com"),
    required_scopes=["read:user_data"],
    resource="https://your-server.com/mcp",              # 限制为面向此服务端的令牌（RFC 8707）
    token_introspection_overrides={
        "cache_ttl_seconds": 300,       # 将内省结果缓存 5 分钟
        "max_cache_size": 1000,         # 最大缓存令牌数
        "timeout_seconds": 15,          # HTTP 请求超时
    },
)

mcp = FastMCP(name="My PropelAuth Protected Server", auth=auth)

Permit.io 身份认证 🤝 FastMCP

Scalekit 🤝 FastMCP

x

---

前置条件
第 1 步：在 Scalekit 环境中配置 MCP 服务端
第 2 步：向 FastMCP 服务端添加身份认证
Testing
启动 MCP 服务端
生产配置
能力
调试
令牌检查
认证
Scalekit 🤝 FastMCP

使用 Scalekit 保护你的 FastMCP 服务端

版本
2.13.0
新增
使用 Scalekit 和 Remote OAuth 模式为 FastMCP 服务端安装身份认证栈：Scalekit 处理用户身份认证，MCP 服务端验证已签发的令牌。

前置条件
开始之前：
获取 Scalekit 账号，并从 Dashboard > Settings 中取得 Environment URL。
准备好 FastMCP 服务端的基础 URL（开发时可以是 localhost，例如 http://localhost:8000/）

第 1 步：在 Scalekit 环境中配置 MCP 服务端
1

注册 MCP 服务端并设置环境

在 Scalekit dashboard 中：
打开 MCP Servers 部分，然后选择 Create new server
输入服务端详情：名称、资源标识符，以及所需的 MCP 客户端身份认证设置
保存，然后复制 Resource ID（例如 res_92015146095）
在 FastMCP 项目的 .env 中：
SCALEKIT_ENVIRONMENT_URL=<YOUR_APP_ENVIRONMENT_URL>
SCALEKIT_RESOURCE_ID=<YOUR_APP_RESOURCE_ID> # res_926EXAMPLE5878
BASE_URL=http://localhost:8000/
# 可选：令牌必须具备的额外 scope
# SCALEKIT_REQUIRED_SCOPES=read,write

第 2 步：向 FastMCP 服务端添加身份认证
创建 FastMCP 服务端文件，并使用 ScalekitProvider 自动处理全部 OAuth 集成：
警告： 旧版 mcp_url 和 client_id 参数已弃用，并将在未来版本中移除。请使用 base_url 替代 mcp_url，并从配置中移除 client_id。
server.py
from fastmcp import FastMCP
from fastmcp.server.auth.providers.scalekit import ScalekitProvider

# 发现 Scalekit 端点并设置 JWT 令牌验证
auth_provider = ScalekitProvider(
    environment_url=SCALEKIT_ENVIRONMENT_URL,    # Scalekit environment URL
    resource_id=SCALEKIT_RESOURCE_ID,            # 资源服务端 ID
    base_url=SERVER_URL,                         # 公共 MCP 端点
    required_scopes=["read"],                    # 可选 scope 强制检查
)

# 创建带身份认证的 FastMCP 服务端
mcp = FastMCP(name="My Scalekit Protected Server", auth=auth_provider)

@mcp.tool
def auth_status() -> dict:
    """显示 Scalekit 身份认证状态。"""
    # 从 JWT 中提取用户 claims
    return {
        "message": "This tool requires authentication via Scalekit",
        "authenticated": True,
        "provider": "Scalekit"
    }

当你需要令牌携带特定权限时，请设置 required_scopes。保持未设置则允许为该资源签发的任何令牌。

Testing

启动 MCP 服务端
uv run python server.py

使用任意 MCP 客户端（例如 mcp-inspector、Claude、VS Code 或 Windsurf）连接正在运行的服务端。确认身份认证成功，并且请求按预期获得授权。

生产配置
对于生产部署，请从环境变量加载配置：
server.py
import os
from fastmcp import FastMCP
from fastmcp.server.auth.providers.scalekit import ScalekitProvider

# 从环境变量加载配置
auth = ScalekitProvider(
    environment_url=os.environ.get("SCALEKIT_ENVIRONMENT_URL"),
    resource_id=os.environ.get("SCALEKIT_RESOURCE_ID"),
    base_url=os.environ.get("BASE_URL", "https://your-server.com")
)

mcp = FastMCP(name="My Scalekit Protected Server", auth=auth)

@mcp.tool
def protected_action() -> str:
    """需要身份认证的工具。"""
    return "Access granted via Scalekit!"

能力
Scalekit 支持面向 MCP 客户端和企业 SSO 的 OAuth 2.1 与 Dynamic Client Registration，并提供内置 JWT 验证和安全控制。
OAuth 2.1/DCR：客户端可自注册，使用 PKCE，并在没有预置凭据的情况下配合 Remote OAuth 模式工作。
验证和 SSO：令牌会经过验证（密钥、RS256、issuer、audience、过期时间），并支持 SAML、OIDC、OAuth 2.0、ADFS、Azure AD 和 Google Workspace；生产环境请使用 HTTPS，并按需查看身份认证日志。

调试
启用详细日志以排查身份认证问题：
import logging
logging.basicConfig(level=logging.DEBUG)

令牌检查
你可以在工具中检查 JWT 令牌，以了解用户上下文：
from fastmcp.server.context import request_ctx
import jwt

@mcp.tool
def inspect_token() -> dict:
    """检查当前 JWT 令牌 claims。"""
    context = request_ctx.get()

    # 从 Authorization header 提取令牌
    if hasattr(context, 'request') and hasattr(context.request, 'headers'):
        auth_header = context.request.headers.get('authorization', '')
        if auth_header.startswith('Bearer '):
            token = auth_header[7:]
            # 不验证直接解码（已由 provider 验证）
            claims = jwt.decode(token, options={"verify_signature": False})
            return claims

    return {"error": "No token found"}

PropelAuth 🤝 FastMCP

Supabase 🤝 FastMCP

x

---

同意界面要求
配置
前置条件
第 1 步：启用 Supabase OAuth Server
第 2 步：获取 Supabase Project URL
第 3 步：FastMCP 配置
测试
运行服务端
使用客户端测试
生产配置
认证
Supabase 🤝 FastMCP

使用 Supabase Auth 保护你的 FastMCP 服务端

版本
2.13.0
新增
本指南展示如何使用 Supabase Auth 保护你的 FastMCP 服务端。该集成使用 Remote OAuth 模式，由 Supabase 处理用户身份认证，而你的 FastMCP 服务端负责验证令牌。
Supabase Auth 目前不支持 RFC 8707 资源指示符，因此 FastMCP 无法验证令牌是否是为特定资源服务端签发的。

同意界面要求
Supabase 的 OAuth Server 会将用户同意界面委托给你的应用。当 MCP 客户端发起授权时，Supabase 会对用户进行身份认证，然后重定向到你应用中配置的回调 URL（例如 https://your-app.com/oauth/callback?authorization_id=...）。你的应用必须托管一个页面，调用 Supabase 的 approveAuthorization() 或 denyAuthorization() API 来完成流程。
SupabaseProvider 负责资源服务端侧的工作（令牌验证和元数据），但你需要单独构建并托管同意界面。有关实现授权页面的详细信息，请参阅 Supabase 的 OAuth Server 文档。

配置

前置条件
开始之前，你需要：
一个带有项目的 Supabase 账号，或一个自托管的 Supabase Auth 实例
在 Supabase Dashboard 中启用 OAuth Server（Authentication → OAuth Server）
在同一设置中启用 Dynamic Client Registration
在你配置的授权路径上托管一个同意界面（见上文）
你的 FastMCP 服务端 URL（开发时可以是 localhost，例如 http://localhost:8000）

第 1 步：启用 Supabase OAuth Server
在 Supabase Dashboard 中：
前往 Authentication → OAuth Server
启用 OAuth Server
将 Site URL 设置为托管同意界面的位置
设置 Authorization Path（例如 /oauth/callback）
启用 Allow Dynamic OAuth Apps，用于 MCP 客户端注册

第 2 步：获取 Supabase Project URL
在 Supabase Dashboard 中：
前往 Project Settings
复制你的 Project URL（例如 https://abc123.supabase.co）

第 3 步：FastMCP 配置
使用 SupabaseProvider 创建 FastMCP 服务端：
server.py
from fastmcp import FastMCP
from fastmcp.server.auth.providers.supabase import SupabaseProvider

auth = SupabaseProvider(
    project_url="https://abc123.supabase.co",
    base_url="http://localhost:8000",
)

mcp = FastMCP("Supabase Protected Server", auth=auth)

@mcp.tool
def protected_tool(message: str) -> str:
    """此工具需要身份认证。"""
    return f"Authenticated user says: {message}"

if __name__ == "__main__":
    mcp.run(transport="http", port=8000)

测试

运行服务端
使用 HTTP 传输启动 FastMCP 服务端，以启用 OAuth 流程：
fastmcp run server.py --transport http --port 8000

使用客户端测试
创建一个测试客户端，对受 Supabase 保护的服务端进行身份认证：
client.py
from fastmcp import Client
import asyncio

async def main():
    async with Client("http://localhost:8000/mcp", auth="oauth") as client:
        print("Authenticated with Supabase!")

        result = await client.call_tool("protected_tool", {"message": "Hello!"})
        print(result)

if __name__ == "__main__":
    asyncio.run(main())

首次运行客户端时：
浏览器会打开 Supabase 的授权端点
完成身份认证后，Supabase 会重定向到你的同意界面
你批准后，客户端会收到令牌，并可以发起已认证的请求

生产配置
对于生产部署，请从环境变量加载配置：
server.py
import os
from fastmcp import FastMCP
from fastmcp.server.auth.providers.supabase import SupabaseProvider

auth = SupabaseProvider(
    project_url=os.environ["SUPABASE_PROJECT_URL"],
    base_url=os.environ.get("BASE_URL", "https://your-server.com"),
)

mcp = FastMCP(name="Supabase Secured App", auth=auth)

Scalekit 🤝 FastMCP

WorkOS 🤝 FastMCP

x

---

配置
先决条件
步骤 1：创建 WorkOS OAuth 应用
步骤 2：FastMCP 配置
测试
运行服务器
使用客户端测试
生产环境配置
环境变量
提供程序选择
WorkOS 特定配置
配置选项
认证
WorkOS 🤝 FastMCP

使用 WorkOS Connect 对 FastMCP 服务器进行身份验证

版本
2.12.0
新增
使用 WorkOS Connect 身份验证保护您的 FastMCP 服务器。此集成使用 OAuth 代理模式通过 WorkOS Connect 处理身份验证，同时保持与 MCP 客户端的兼容性。
本指南涵盖 WorkOS Connect 应用程序。对于使用 AuthKit 的动态客户端注册 (DCR)，请参阅 AuthKit 集成。

配置

先决条件
开始之前，您需要：
一个有权创建 OAuth 应用的 WorkOS 账户
您的 FastMCP 服务器 URL（开发时可以是 localhost，例如 http://localhost:8000）

步骤 1：创建 WorkOS OAuth 应用
在您的 WorkOS 仪表板中创建 OAuth 应用以获取身份验证所需的凭据：
1

创建 OAuth 应用程序

在您的 WorkOS 仪表板中：
导航到 Applications
点击 Create Application
选择 OAuth Application
为您的应用程序命名
2

获取凭据

在您的 OAuth 应用程序设置中：
复制您的 Client ID（以 client_ 开头）
点击 Generate Client Secret 并安全保存
复制您的 AuthKit Domain（例如，https://your-app.authkit.app）
3

配置重定向 URI

在 Redirect URIs 部分：
添加：http://localhost:8000/auth/callback（用于开发）
对于生产环境，添加您服务器的公共 URL + /auth/callback
回调 URL 必须完全匹配。默认路径是 /auth/callback，但您可以使用 redirect_path 参数自定义它。

步骤 2：FastMCP 配置
使用 WorkOSProvider 创建您的 FastMCP 服务器：
server.py
from fastmcp import FastMCP
from fastmcp.server.auth.providers.workos import WorkOSProvider

# 配置 WorkOS OAuth
auth = WorkOSProvider(
    client_id="client_YOUR_CLIENT_ID",
    client_secret="YOUR_CLIENT_SECRET",
    authkit_domain="https://your-app.authkit.app",
    base_url="http://localhost:8000",
    required_scopes=["openid", "profile", "email"]
)

mcp = FastMCP("WorkOS 受保护服务器", auth=auth)

@mcp.tool
def protected_tool(message: str) -> str:
    """此工具需要身份验证。"""
    return f"已认证用户说：{message}"

if __name__ == "__main__":
    mcp.run(transport="http", port=8000)

测试

运行服务器
使用 HTTP 传输启动您的 FastMCP 服务器以启用 OAuth 流程：
fastmcp run server.py --transport http --port 8000

您的服务器现在正在运行并受到 WorkOS OAuth 身份验证的保护。

使用客户端测试
创建一个测试客户端，与您的 WorkOS 保护的服务器进行身份验证：
client.py
from fastmcp import Client
import asyncio

async def main():    
    # 客户端将自动处理 WorkOS OAuth
    async with Client("http://localhost:8000/mcp", auth="oauth") as client:
        # 首次连接将在您的浏览器中打开 WorkOS 登录页面
        print("✓ 已通过 WorkOS 身份验证！")
        
        # 测试受保护的工具
        result = await client.call_tool("protected_tool", {"message": "Hello!"})
        print(result)

if __name__ == "__main__":
    asyncio.run(main())

当您第一次运行客户端时：
您的浏览器将打开到 WorkOS 的授权页面
在您授权应用程序后，您将被重定向回来
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
from fastmcp.server.auth.providers.workos import WorkOSProvider
from key_value.aio.stores.redis import RedisStore
from key_value.aio.wrappers.encryption import FernetEncryptionWrapper
from cryptography.fernet import Fernet

# 使用加密的持久化令牌存储进行生产配置
auth = WorkOSProvider(
    client_id="client_YOUR_CLIENT_ID",
    client_secret="YOUR_CLIENT_SECRET",
    authkit_domain="https://your-app.authkit.app",
    base_url="https://your-production-domain.com",
    required_scopes=["openid", "profile", "email"],

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

mcp = FastMCP(name="Production WorkOS App", auth=auth)

jwt_signing_key 与 client_storage 需要配合使用，才能在服务器重启后保留令牌与客户端注册信息。请使用 FernetEncryptionWrapper 加密存储中的敏感 OAuth 令牌，否则将以明文保存。建议通过环境变量传递密钥，并使用 Redis 等持久化后端以支持分布式部署。
更多参数详情请参阅 OAuth 代理文档。

环境变量
版本
2.12.1
新增
对于生产部署，请使用环境变量而不是硬编码凭据。

提供程序选择
设置此环境变量允许自动使用 WorkOS 提供程序，而无需在代码中显式实例化它。

FASTMCP_SERVER_AUTH
默认值:"未设置"
设置为 fastmcp.server.auth.providers.workos.WorkOSProvider 以使用 WorkOS 身份验证。

WorkOS 特定配置
这些环境变量为 WorkOS 提供程序提供默认值，无论它是手动实例化的还是通过 FASTMCP_SERVER_AUTH 配置的。

FASTMCP_SERVER_AUTH_WORKOS_CLIENT_ID
必填
您的 WorkOS OAuth 应用客户端 ID（例如，client_01K33Y6GGS7T3AWMPJWKW42Y3Q）

FASTMCP_SERVER_AUTH_WORKOS_CLIENT_SECRET
必填
您的 WorkOS OAuth 应用客户端密钥

FASTMCP_SERVER_AUTH_WORKOS_AUTHKIT_DOMAIN
必填
您的 WorkOS AuthKit 域（例如，https://your-app.authkit.app）

FASTMCP_SERVER_AUTH_WORKOS_BASE_URL
默认值:"http://localhost:8000"
OAuth 端点可访问的公共 URL（包含任何挂载路径）

FASTMCP_SERVER_AUTH_WORKOS_ISSUER_URL
默认值:"使用 BASE_URL"
用于 OAuth 元数据的 Issuer URL（默认等于 BASE_URL）。当挂载在路径前缀下时，请设置为根级 URL 以避免 404 日志。详见 HTTP 部署指南。

FASTMCP_SERVER_AUTH_WORKOS_REDIRECT_PATH
默认值:"/auth/callback"
在您的 WorkOS OAuth 应用中配置的重定向路径

FASTMCP_SERVER_AUTH_WORKOS_REQUIRED_SCOPES
默认值:"[]"
必需的 OAuth 作用域的逗号、空格或 JSON 分隔列表（例如，openid profile email 或 ["openid","profile","email"]）

FASTMCP_SERVER_AUTH_WORKOS_TIMEOUT_SECONDS
默认值:"10"
WorkOS API 调用的 HTTP 请求超时
示例 .env 文件：
# WorkOS OAuth 凭据（始终用作默认值）
FASTMCP_SERVER_AUTH_WORKOS_CLIENT_ID=client_01K33Y6GGS7T3AWMPJWKW42Y3Q
FASTMCP_SERVER_AUTH_WORKOS_CLIENT_SECRET=your_client_secret
FASTMCP_SERVER_AUTH_WORKOS_AUTHKIT_DOMAIN=https://your-app.authkit.app
FASTMCP_SERVER_AUTH_WORKOS_BASE_URL=https://your-server.com
FASTMCP_SERVER_AUTH_WORKOS_REQUIRED_SCOPES=["openid","profile","email"]

# 可选：为所有服务器自动配置 WorkOS 身份验证
FASTMCP_SERVER_AUTH=fastmcp.server.auth.providers.workos.WorkOSProvider

设置环境变量后，您可以选择：
选项 1：手动实例化（环境变量提供默认值）
server.py
from fastmcp import FastMCP
from fastmcp.server.auth.providers.workos import WorkOSProvider

# 环境变量为 WorkOSProvider() 提供默认值
auth = WorkOSProvider()  # 使用环境变量默认值
mcp = FastMCP(name="WorkOS 受保护服务器", auth=auth)

选项 2：自动配置（需要 FASTMCP_SERVER_AUTH=fastmcp.server.auth.providers.workos.WorkOSProvider）
server.py
from fastmcp import FastMCP

# 身份验证从 FASTMCP_SERVER_AUTH 自动配置
mcp = FastMCP(name="WorkOS 受保护服务器")

配置选项

client_id
必填
WorkOS OAuth 应用程序客户端 ID

client_secret
必填
WorkOS OAuth 应用程序客户端密钥

authkit_domain
必填
您的 WorkOS AuthKit 域 URL（例如，https://your-app.authkit.app）

base_url
必填
您的 FastMCP 服务器的公共 URL

required_scopes
默认值:"[]"
要请求的 OAuth 作用域

redirect_path
默认值:"/auth/callback"
OAuth 回调路径

timeout_seconds
默认值:"10"
API 请求超时
Supabase 🤝 FastMCP

FastAPI 🤝 FastMCP

x

---
