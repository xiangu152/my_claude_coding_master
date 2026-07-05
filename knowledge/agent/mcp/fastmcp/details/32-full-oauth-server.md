---
title: "完整 OAuth 服务器"
source: "https://fastmcp.wiki/zh/servers/auth/full-oauth-server"
version: "latest"
---

# 完整 OAuth 服务器

> 原始文档来源：https://fastmcp.wiki/zh/servers/auth/full-oauth-server

---

OAuth 提供商(OAuthProvider)
必需实现
客户端管理
授权流程
令牌管理
身份认证
完整 OAuth 服务器

构建一个自包含的身份验证系统，您的 FastMCP 服务器在其中管理用户、发行令牌并验证它们。

版本
2.11.0
新增
**这是一个大多数用户应该避免的极其高级的模式。**构建安全的 OAuth 2.1 服务器需要在身份验证协议、密码学和安全最佳实践方面具备深厚的专业知识。复杂性远超初始实现，包括持续的安全监控、威胁响应和合规维护。
请改用远程 OAuth，除非您有外部身份提供商无法满足的令人信服的要求，例如空隙环境或专门的合规需求。
完整 OAuth 服务器模式的存在是为了支持 MCP 协议规范的要求。您的 FastMCP 服务器成为授权服务器和资源服务器，处理从用户登录到令牌验证的完整身份验证生命周期。
本文档的存在是为了完整性——绝大多数应用程序应该改用外部身份提供商。

OAuth 提供商(OAuthProvider)
FastMCP 提供了实现 OAuth 2.1 规范的 OAuthProvider 抽象类。要使用此模式，您必须子类化 OAuthProvider 并实现所有必需的抽象方法。
OAuthProvider 处理 OAuth 端点、协议流和安全要求，但将所有存储、用户管理和业务逻辑委托给您对抽象方法的实现。

必需实现
您必须实现这些抽象方法来创建一个可正常工作的 OAuth 服务器：

客户端管理
客户端管理方法

get_client
async method
从数据库中按 ID 检索客户端信息。

显示 参数

显示 返回

register_client
async method
在数据库中存储新的客户端注册信息。

显示 参数

显示 返回

授权流程
授权流程方法

authorize
async method
处理授权请求并返回重定向 URL。必须实现用户身份验证和同意收集。

显示 参数

显示 返回

load_authorization_code
async method
通过代码字符串从存储中加载授权代码。如果代码无效或已过期，返回 None。

显示 参数

显示 返回

令牌管理
令牌管理方法

exchange_authorization_code
async method
用授权代码交换访问和刷新令牌。必须验证代码并创建新令牌。

显示 参数

显示 返回

load_refresh_token
async method
通过令牌字符串从存储中加载刷新令牌。如果令牌无效或已过期，返回 None。

显示 参数

显示 返回

exchange_refresh_token
async method
用刷新令牌交换新的访问/刷新令牌对。必须验证范围和令牌。

显示 参数

显示 返回

load_access_token
async method
通过其令牌字符串加载访问令牌。

显示 参数

显示 返回

revoke_token
async method
撤销访问或刷新令牌，在存储中将其标记为无效。

显示 参数

显示 返回

verify_token
async method
验证传入请求的承载令牌。如果有效则返回 AccessToken，如果无效则返回 None。

显示 参数

显示 返回

每个方法必须根据 OAuth 2.1 规范处理存储、验证、安全和错误情况。实现复杂性很大，需要 OAuth 安全考虑方面的专业知识。
安全通知： OAuth 服务器实现涉及众多安全考虑，包括 PKCE、状态参数、重定向 URI 验证、令牌绑定、重放攻击预防和安全存储要求。错误可能导致严重的安全漏洞。
OIDC 代理

多个认证来源

x

