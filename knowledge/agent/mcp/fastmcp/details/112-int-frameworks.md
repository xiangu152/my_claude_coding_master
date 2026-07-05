---
title: "集成: 框架 (FastAPI, OpenAPI, Pydantic AI)"
source: "https://fastmcp.wiki/zh/integrations/fastapi"
version: "latest"
---

# 集成: 框架 (FastAPI, OpenAPI, Pydantic AI)

> 原始文档来源：https://fastmcp.wiki/zh/integrations/fastapi (FastMCP 集成文档)

---

示例 FastAPI 应用程序
生成 MCP 服务器
基本转换
添加组件
与 MCP 服务器交互
自定义路由映射
身份验证和请求头
挂载 MCP 服务器
基本挂载
提供 LLM 友好的 API
关键考虑事项
操作 ID
生命周期管理
CORS 中间件
合并生命周期
性能提示
Web 框架
FastAPI 🤝 FastMCP

将 FastMCP 与 FastAPI 应用程序集成

2.11 中的新功能：FastMCP 正在引入下一代 OpenAPI 解析器。新解析器在性能和兼容性方面大大改进，也更容易维护。要启用它，请设置环境变量 FASTMCP_EXPERIMENTAL_ENABLE_NEW_OPENAPI_PARSER=true。
新解析器在 API 方面与现有实现大体兼容，并将在未来版本中成为默认选项。我们鼓励所有用户在它成为默认选项之前测试它并报告任何问题。
FastMCP 提供了两种强大的方式来与 FastAPI 应用程序集成：
从您的 FastAPI 应用程序生成 MCP 服务器 - 将现有的 API 端点转换为 MCP 工具
将 MCP 服务器挂载到您的 FastAPI 应用程序中 - 向您的 Web 应用程序添加 MCP 功能
从 OpenAPI 生成 MCP 服务器是开始使用 FastMCP 的好方法，但在实践中，LLM 在精心设计和策划的 MCP 服务器上的性能表现显著优于自动转换的 OpenAPI 服务器。这对于具有许多端点和参数的复杂 API 尤其如此。
我们建议使用 FastAPI 集成进行引导和原型制作，而不是将您的 API 镜像给 LLM 客户端。有关更多详细信息，请参阅文章 停止将您的 REST API 转换为 MCP。
FastMCP 不包含 FastAPI 作为依赖；您必须单独安装它才能使用此集成。

示例 FastAPI 应用程序
在整个指南中，我们将使用这个电子商务 API 作为示例（点击 复制 按钮可复制它以便在其他代码块中使用）：
# 将此 FastAPI 服务器复制到本指南的其他代码块中

from fastapi import FastAPI, HTTPException
from pydantic import BaseModel

# 模型
class Product(BaseModel):
    name: str
    price: float
    category: str
    description: str | None = None

class ProductResponse(BaseModel):
    id: int
    name: str
    price: float
    category: str
    description: str | None = None

# 创建 FastAPI 应用程序
app = FastAPI(title="电子商务 API", version="1.0.0")

# 内存数据库
products_db = {
    1: ProductResponse(
        id=1, name="笔记本电脑", price=999.99, category="电子产品"
    ),
    2: ProductResponse(
        id=2, name="鼠标", price=29.99, category="电子产品"
    ),
    3: ProductResponse(
        id=3, name="办公椅", price=299.99, category="家具"
    ),
}
next_id = 4

@app.get("/products", response_model=list[ProductResponse])
def list_products(
    category: str | None = None,
    max_price: float | None = None,
) -> list[ProductResponse]:
    """列出所有产品，支持可选过滤。"""
    products = list(products_db.values())
    if category:
        products = [p for p in products if p.category == category]
    if max_price:
        products = [p for p in products if p.price <= max_price]
    return products

@app.get("/products/{product_id}", response_model=ProductResponse)
def get_product(product_id: int):
    """根据 ID 获取特定产品。"""
    if product_id not in products_db:
        raise HTTPException(status_code=404, detail="未找到产品")
    return products_db[product_id]

@app.post("/products", response_model=ProductResponse)
def create_product(product: Product):
    """创建新产品。"""
    global next_id
    product_response = ProductResponse(id=next_id, **product.model_dump())
    products_db[next_id] = product_response
    next_id += 1
    return product_response

@app.put("/products/{product_id}", response_model=ProductResponse)
def update_product(product_id: int, product: Product):
    """更新现有产品。"""
    if product_id not in products_db:
        raise HTTPException(status_code=404, detail="未找到产品")
    products_db[product_id] = ProductResponse(
        id=product_id,
        **product.model_dump(),
    )
    return products_db[product_id]

@app.delete("/products/{product_id}")
def delete_product(product_id: int):
    """删除产品。"""
    if product_id not in products_db:
        raise HTTPException(status_code=404, detail="未找到产品")
    del products_db[product_id]
    return {"message": "产品已删除"}

See all 83 lines
本指南中的所有后续代码示例都假设您已经定义了上述 FastAPI 应用程序代码。每个示例都基于这个基础应用程序 app 构建。

生成 MCP 服务器
版本
2.0.0
新增
引导 MCP 服务器最常见的方法之一是从现有的 FastAPI 应用程序生成它。FastMCP 将您的 FastAPI 端点暴露为 MCP 组件（默认为工具），以便将您的 API 暴露给 LLM 客户端。

基本转换
用一行代码将 FastAPI 应用程序转换为 MCP 服务器：
# 假设上面的 FastAPI 应用程序已经定义
from fastmcp import FastMCP

# 转换为 MCP 服务器
mcp = FastMCP.from_fastapi(app=app)

if __name__ == "__main__":
    mcp.run()

添加组件
您转换的 MCP 服务器是一个完整的 FastMCP 实例，这意味着您可以像处理任何其他 FastMCP 实例一样向其添加新工具、资源和其他组件。
# 假设上面的 FastAPI 应用程序已经定义
from fastmcp import FastMCP

# 转换为 MCP 服务器
mcp = FastMCP.from_fastapi(app=app)

# 添加新工具
@mcp.tool
def get_product(product_id: int) -> ProductResponse:
    """根据 ID 获取产品。"""
    return products_db[product_id]

# 运行 MCP 服务器
if __name__ == "__main__":
    mcp.run()

与 MCP 服务器交互
一旦您将 FastAPI 应用程序转换为 MCP 服务器，您可以使用 FastMCP 客户端与其交互，在将其部署到基于 LLM 的应用程序之前测试功能。
# 假设上面的 FastAPI 应用程序已经定义
from fastmcp import FastMCP
from fastmcp.client import Client
import asyncio

# 转换为 MCP 服务器
mcp = FastMCP.from_fastapi(app=app)

async def demo():
    async with Client(mcp) as client:
        # 列出可用工具
        tools = await client.list_tools()
        print(f"可用工具: {[t.name for t in tools]}")
        
        # 创建产品
        result = await client.call_tool(
            "create_product_products_post",
            {
                "name": "无线键盘",
                "price": 79.99,
                "category": "电子产品",
                "description": "蓝牙机械键盘"
            }
        )
        print(f"已创建产品: {result.data}")
        
        # 列出价格低于 $100 的电子产品
        result = await client.call_tool(
            "list_products_products_get",
            {"category": "电子产品", "max_price": 100}
        )
        print(f"经济实惠的电子产品: {result.data}")

if __name__ == "__main__":
    asyncio.run(demo())

自定义路由映射
因为 FastMCP 的 FastAPI 集成基于其 OpenAPI 集成，您可以以完全相同的方式自定义端点如何转换为 MCP 组件。例如，这里我们使用 RouteMap 将所有 GET 请求映射到 MCP 资源，将所有 POST/PUT/DELETE 请求映射到 MCP 工具：
# 假设上面的 FastAPI 应用程序已经定义
from fastmcp import FastMCP
from fastmcp.server.openapi import RouteMap, MCPType

# 如果使用实验性解析器，请从实验模块导入：
# from fastmcp.experimental.server.openapi import RouteMap, MCPType

# 自定义映射规则
mcp = FastMCP.from_fastapi(
    app=app,
    route_maps=[
        # 带有路径参数的 GET → ResourceTemplates
        RouteMap(
            methods=["GET"], 
            pattern=r".*\{.*\}.*", 
            mcp_type=MCPType.RESOURCE_TEMPLATE
        ),
        # 其他 GET → Resources
        RouteMap(
            methods=["GET"], 
            pattern=r".*", 
            mcp_type=MCPType.RESOURCE
        ),
        # POST/PUT/DELETE → Tools（默认）
    ],
)

# 现在：
# - GET /products → Resource
# - GET /products/{id} → ResourceTemplate
# - POST/PUT/DELETE → Tools

要了解更多关于自定义转换过程的信息，请参阅 OpenAPI 集成指南。

身份验证和请求头
您可以通过 httpx_client_kwargs 参数配置请求头和其他客户端选项。例如，要为您的 FastAPI 应用程序添加身份验证，您可以将 headers 字典传递给 httpx_client_kwargs 参数：
# 假设上面的 FastAPI 应用程序已经定义
from fastmcp import FastMCP

# 为您的 FastAPI 应用程序添加身份验证
from fastapi import Depends, Header
from fastapi.security import HTTPBearer, HTTPAuthorizationCredentials

security = HTTPBearer()

def verify_token(credentials: HTTPAuthorizationCredentials = Depends(security)):
    if credentials.credentials != "secret-token":
        raise HTTPException(status_code=401, detail="无效的身份验证")
    return credentials.credentials

# 添加受保护的端点
@app.get("/admin/stats", dependencies=[Depends(verify_token)])
def get_admin_stats():
    return {
        "total_products": len(products_db),
        "categories": list(set(p.category for p in products_db.values()))
    }

# 使用身份验证请求头创建 MCP 服务器
mcp = FastMCP.from_fastapi(
    app=app,
    httpx_client_kwargs={
        "headers": {
            "Authorization": "Bearer secret-token",
        }
    }
)

挂载 MCP 服务器
版本
2.3.1
新增
除了生成服务器之外，FastMCP 还可以帮助您将 MCP 服务器添加到现有的 FastAPI 应用程序中。您可以通过挂载 MCP ASGI 应用程序来实现这一点。

基本挂载
要挂载 MCP 服务器，您可以在 FastMCP 实例上使用 http_app 方法。这将返回一个可以挂载到您的 FastAPI 应用程序的 ASGI 应用程序。
from fastmcp import FastMCP
from fastapi import FastAPI

# 创建 MCP 服务器
mcp = FastMCP("分析工具")

@mcp.tool
def analyze_pricing(category: str) -> dict:
    """分析某个类别的定价。"""
    products = [p for p in products_db.values() if p.category == category]
    if not products:
        return {"error": f"{category} 中没有产品"}
    
    prices = [p.price for p in products]
    return {
        "category": category,
        "avg_price": round(sum(prices) / len(prices), 2),
        "min": min(prices),
        "max": max(prices),
    }

# 从 MCP 服务器创建 ASGI 应用程序
mcp_app = mcp.http_app(path='/mcp')

# 关键：将生命周期传递给 FastAPI
app = FastAPI(title="电子商务 API", lifespan=mcp_app.lifespan)

# 挂载 MCP 服务器
app.mount("/analytics", mcp_app)

# 现在：API 在 /products/*，MCP 在 /analytics/mcp/

提供 LLM 友好的 API
一种常见的模式是从FastAPI应用程序生成MCP服务器，并从同一应用程序提供两个接口。这为您的常规API提供了一个LLM优化的接口：
# 假设上面的 FastAPI 应用程序已经定义
from fastmcp import FastMCP
from fastapi import FastAPI

# 1. 从您的 API 生成 MCP 服务器
mcp = FastMCP.from_fastapi(app=app, name="电子商务 MCP")

# 2. 创建 MCP 的 ASGI 应用程序
mcp_app = mcp.http_app(path='/mcp')

# 3. 创建一个结合两组路由的新 FastAPI 应用程序
combined_app = FastAPI(
    title="带有 MCP 的电子商务 API",
    routes=[
        *mcp_app.routes,  # MCP 路由
        *app.routes,      # 原始 API 路由
    ],
    lifespan=mcp_app.lifespan,
)

# 现在您拥有：
# - 常规 API：http://localhost:8000/products
# - LLM-friendly MCP: http://localhost:8000/mcp/
# 两者都从同一个 FastAPI 应用程序提供服务！

这种方法让您可以维护单一代码库，同时为 LLM 客户端提供传统的 REST 端点和 MCP 兼容的端点。

关键考虑事项

操作 ID
FastAPI 操作 ID 成为 MCP 组件名称。始终指定有意义的操作 ID：
# 好的做法 - 明确的 operation_id
@app.get("/users/{user_id}", operation_id="get_user_by_id")
def get_user(user_id: int):
    return {"id": user_id}

# 不太理想 - 自动生成的名称
@app.get("/users/{user_id}")
def get_user(user_id: int):
    return {"id": user_id}

生命周期管理
挂载 MCP 服务器时，始终传递生命周期上下文：
# 正确 - 传递了生命周期
mcp_app = mcp.http_app(path='/mcp')
app = FastAPI(lifespan=mcp_app.lifespan)
app.mount("/mcp", mcp_app)

# 错误 - 缺少生命周期
app = FastAPI()
app.mount("/mcp", mcp.http_app())  # 会话管理器不会初始化

如果您在路径前缀下挂载启用身份验证的 MCP 服务器，请参阅挂载已认证服务器，了解关键的 OAuth 路由注意事项。

CORS 中间件
如果您的 FastAPI 应用使用了 CORSMiddleware，并且挂载了受 OAuth 保护的 FastMCP 服务器，请避免在整个应用上添加全局 CORS 中间件。FastMCP 与 MCP SDK 已经为 OAuth 路由处理了 CORS，叠加中间件可能导致冲突（例如 .well-known 路由或 OPTIONS 请求返回 404）。
如果需要为自有 FastAPI 路由开启 CORS，请采用子应用模式：分别挂载您的 API 与 FastMCP，每个子应用使用各自的中间件，而不是在组合后的应用上添加顶层 CORSMiddleware。

合并生命周期
如果您的 FastAPI 应用程序已经有生命周期（用于数据库连接、启动任务等），您不能简单地用 MCP 生命周期替换它。相反，您需要创建一个管理两个上下文的新生命周期函数。这确保您的应用程序初始化逻辑和 MCP 服务器的会话管理器都能正常运行：
from contextlib import asynccontextmanager
from fastapi import FastAPI
from fastmcp import FastMCP

# 您现有的生命周期
@asynccontextmanager
async def app_lifespan(app: FastAPI):
    # 启动
    print("正在启动应用程序...")
    # 初始化数据库、缓存等
    yield
    # 关闭
    print("正在关闭应用程序...")

# 创建 MCP 服务器
mcp = FastMCP("工具")
mcp_app = mcp.http_app(path='/mcp')

# 合并两个生命周期
@asynccontextmanager
async def combined_lifespan(app: FastAPI):
    # 运行两个生命周期
    async with app_lifespan(app):
        async with mcp_app.lifespan(app):
            yield

# 使用合并的生命周期
app = FastAPI(lifespan=combined_lifespan)
app.mount("/mcp", mcp_app)

这个模式确保您的应用程序初始化逻辑和 MCP 服务器的会话管理器都得到正确管理。关键是使用嵌套的 async with 语句 - 内部上下文（MCP）将在外部上下文（您的应用程序）之后初始化，并在它之前清理。这为您的所有资源维护了正确的初始化和清理顺序。

性能提示
使用内存传输进行测试 - 将 MCP 服务器直接传递给客户端
设计专用的 MCP 工具 - 比自动转换复杂 API 更好
保持工具参数简单 - LLM 在专注的接口上表现更好
有关配置选项的更多详细信息，请参阅 OpenAPI 集成指南。
WorkOS 🤝 FastMCP

OpenAPI 🤝 FastMCP

x

---

创建服务器
身份验证
路由映射
自定义路由映射
排除路由
高级路由映射
自定义
组件名称
标签
RouteMap 标签
全局标签
客户端元数据中的 OpenAPI 标签
高级自定义
请求参数处理
查询参数
路径参数
数组参数
请求头
Web 框架
OpenAPI 🤝 FastMCP

从任何 OpenAPI 规范生成 MCP 服务器

版本
2.0.0
新增
2.11 中的新功能：FastMCP 正在引入下一代 OpenAPI 解析器。新解析器在性能和兼容性方面大幅改进，也更容易维护。要启用它，请设置环境变量 FASTMCP_EXPERIMENTAL_ENABLE_NEW_OPENAPI_PARSER=true。
新解析器在 API 方面与现有实现大体兼容，并将在未来版本中成为默认选项。我们鼓励所有用户在它成为默认选项之前测试它并报告任何问题。
FastMCP 可以从任何 OpenAPI 规范自动生成 MCP 服务器，允许 AI 模型通过 MCP 协议与现有 API 交互。无需手动创建工具和资源，您只需提供 OpenAPI 规范，FastMCP 就会智能地将 API 端点转换为适当的 MCP 组件。
从 OpenAPI 生成 MCP 服务器是开始使用 FastMCP 的好方法，但在实践中，精心设计和精心制作的 MCP 服务器比自动转换的 OpenAPI 服务器在 LLM 上实现显著更好的性能。对于具有许多端点和参数的复杂 API，这一点尤其如此。
我们建议使用 FastAPI 集成进行引导和原型设计，而不是将您的 API 镜像给 LLM 客户端。有关更多详细信息，请参阅文章 停止将您的 REST API 转换为 MCP。

创建服务器
要将 OpenAPI 规范转换为 MCP 服务器，请使用 FastMCP.from_openapi() 类方法：
server.py
import httpx
from fastmcp import FastMCP

# 为您的 API 创建 HTTP 客户端
client = httpx.AsyncClient(base_url="https://api.example.com")

# 加载您的 OpenAPI 规范
openapi_spec = httpx.get("https://api.example.com/openapi.json").json()

# 创建 MCP 服务器
mcp = FastMCP.from_openapi(
    openapi_spec=openapi_spec,
    client=client,
    name="我的 API 服务器"
)

if __name__ == "__main__":
    mcp.run()

身份验证
如果您的 API 需要身份验证，请在 HTTP 客户端上进行配置：
import httpx
from fastmcp import FastMCP

# Bearer 令牌身份验证
api_client = httpx.AsyncClient(
    base_url="https://api.example.com",
    headers={"Authorization": "Bearer YOUR_TOKEN"}
)

# 使用经过身份验证的客户端创建 MCP 服务器
mcp = FastMCP.from_openapi(
    openapi_spec=spec, 
    client=api_client,
    timeout=30.0  # 所有请求的 30 秒超时
)

路由映射
默认情况下，FastMCP 将 OpenAPI 规范中的每个端点转换为 MCP 工具。这提供了一个简单、可预测的起点，确保您的 API 的所有功能对绝大多数仅支持 MCP 工具的 LLM 客户端立即可用。
虽然这是为了最大兼容性的实用默认设置，但您可以轻松地自定义此行为。在内部，FastMCP 使用有序的 RouteMap 对象列表来确定如何将 OpenAPI 路由映射到各种 MCP 组件类型。
每个 RouteMap 指定方法、模式和标签的组合，以及相应的 MCP 组件类型。每个 OpenAPI 路由都会按顺序检查每个 RouteMap，第一个匹配所有条件的将用于确定其转换的 MCP 类型。特殊类型 EXCLUDE 可用于完全从 MCP 服务器中排除路由。
方法：要匹配的 HTTP 方法（例如 ["GET", "POST"] 或 "*" 表示全部）
模式：用于匹配路由路径的正则表达式模式（例如 r"^/users/.*" 或 r".*" 表示全部）
标签：必须全部存在的 OpenAPI 标签集。空集合（{}）意味着没有标签过滤，因此路由不管其标签如何都会匹配
MCP 类型：要创建的 MCP 组件类型（TOOL、RESOURCE、RESOURCE_TEMPLATE 或 EXCLUDE）
MCP 标签：要添加到从匹配路由创建的组件的自定义标签集
以下是 FastMCP 的默认规则：
from fastmcp.server.openapi import RouteMap, MCPType

DEFAULT_ROUTE_MAPPINGS = [
    # 所有路由都成为工具
    RouteMap(mcp_type=MCPType.TOOL),
]

实验性解析器：如果您使用的是新解析器（通过 FASTMCP_EXPERIMENTAL_ENABLE_NEW_OPENAPI_PARSER=true 启用），请从实验模块导入：
from fastmcp.experimental.server.openapi import RouteMap, MCPType

API 是相同的，但实现提供了更好的性能和无服务器兼容性。

自定义路由映射
在创建 FastMCP 服务器时，您可以通过提供自己的 RouteMap 对象列表来自定义路由行为。您的自定义映射在默认路由映射之前处理，路由将被分配给第一个匹配的自定义映射。
例如，在 FastMCP 2.8.0 之前，GET 请求根据是否具有路径参数自动映射到 Resource 和 ResourceTemplate 组件。（这仅仅是为了客户端兼容性原因而改变的。）您可以通过提供自定义路由映射来恢复此行为：
from fastmcp import FastMCP
from fastmcp.server.openapi import RouteMap, MCPType

# 恢复 2.8.0 之前的语义映射
semantic_maps = [
    # 带有路径参数的 GET 请求成为 ResourceTemplates
    RouteMap(methods=["GET"], pattern=r".*\{.*\}.*", mcp_type=MCPType.RESOURCE_TEMPLATE),
    # 所有其他 GET 请求成为 Resources
    RouteMap(methods=["GET"], pattern=r".*", mcp_type=MCPType.RESOURCE),
]

mcp = FastMCP.from_openapi(
    openapi_spec=spec,
    client=client,
    route_maps=semantic_maps,
)

使用这些映射，GET 请求会被语义化处理，所有其他方法（POST、PUT 等）将转入默认规则并成为 Tool。
以下是一个更完整的示例，使用自定义路由映射将 /analytics/ 下的所有 GET 端点转换为工具，同时排除所有管理员端点和所有标记为“internal”的路由。所有其他路由将由默认规则处理：
from fastmcp import FastMCP
from fastmcp.server.openapi import RouteMap, MCPType

mcp = FastMCP.from_openapi(
    openapi_spec=spec,
    client=client,
    route_maps=[
        # 分析 `GET` 端点是工具
        RouteMap(
            methods=["GET"], 
            pattern=r"^/analytics/.*", 
            mcp_type=MCPType.TOOL,
        ),

        # 排除所有管理员端点
        RouteMap(
            pattern=r"^/admin/.*", 
            mcp_type=MCPType.EXCLUDE,
        ),

        # 排除所有标记为 "internal" 的路由
        RouteMap(
            tags={"internal"},
            mcp_type=MCPType.EXCLUDE,
        ),
    ],
)

默认路由映射始终在您的自定义映射之后应用，因此您不必为每个可能的路由创建路由映射。

排除路由
要从 MCP 服务器中排除路由，请使用路由映射将它们分配给 MCPType.EXCLUDE。
您可以使用此方法通过具体针对敏感或内部路由来删除它们：
from fastmcp import FastMCP
from fastmcp.server.openapi import RouteMap, MCPType

mcp = FastMCP.from_openapi(
    openapi_spec=spec,
    client=client,
    route_maps=[
        RouteMap(pattern=r"^/admin/.*", mcp_type=MCPType.EXCLUDE),
        RouteMap(tags={"internal"}, mcp_type=MCPType.EXCLUDE),
    ],
)

或者您可以使用通配规则来排除您的映射未明确处理的所有内容：
from fastmcp import FastMCP
from fastmcp.server.openapi import RouteMap, MCPType

mcp = FastMCP.from_openapi(
    openapi_spec=spec,
    client=client,
    route_maps=[
        # 自定义映射逻辑在这里
        # ... 您的具体路由映射 ...
        # 排除所有剩余路由
        RouteMap(mcp_type=MCPType.EXCLUDE),
    ],
)

使用通配排除规则将阻止应用默认路由映射，因为它将匹配每个剩余路由。如果您想要明确允许列表特定路由，这很有用。

高级路由映射
版本
2.5.0
新增
对于需要更复杂逻辑的高级用例，您可以提供 route_map_fn 可调用对象。在应用路由映射逻辑后，此函数在每个匹配的路由及其分配的 MCP 组件类型上被调用。它可以选择性地返回不同的组件类型来覆盖映射分配。如果它返回 None，则使用分配的类型。
除了更精确地针对方法、模式和标签之外，此函数还可以访问有关路由的任何其他 OpenAPI 元数据。
FastMCP Cloud对个人服务器免费开放，并为团队提供按需付费的简易计费方案。route_map_fn 会应用于所有路由，即使在自定义映射中匹配了 MCPType.EXCLUDE 的路由。这为你提供了自定义映射或覆盖排除规则的机会。
from fastmcp import FastMCP
from fastmcp.server.openapi import RouteMap, MCPType, HTTPRoute

def custom_route_mapper(route: HTTPRoute, mcp_type: MCPType) -> MCPType | None:
    """高级路由类型映射。"""
    # 无论 HTTP 方法如何，将所有管理员路由转换为工具
    if "/admin/" in route.path:
        return MCPType.TOOL

    elif "internal" in route.tags:
        return MCPType.EXCLUDE
    
    # 即使是 POST，也将用户详细信息路由转换为模板
    elif route.path.startswith("/users/") and route.method == "POST":
        return MCPType.RESOURCE_TEMPLATE
    
    # 对所有其他路由使用默认值
    return None

mcp = FastMCP.from_openapi(
    openapi_spec=spec,
    client=client,
    route_map_fn=custom_route_mapper,
)

自定义

组件名称
版本
2.5.0
新增
FastMCP 根据 OpenAPI 规范自动生成 MCP 组件的名称。默认情况下，它使用 OpenAPI 规范中的 operationId，直到第一个双下划线（__）。
所有组件名称都会自动：
缩略化：空格和特殊字符被转换为下划线或删除
截断：限制为最多 56 个字符以确保兼容性
唯一：如果多个组件具有相同名称，会自动附加数字以使它们唯一
为了更好地控制组件名称，您可以提供一个 mcp_names 字典，将 operationId 值映射到您所需的名称。operationId 必须与在 OpenAPI 规范中显示的完全一致。提供的名称始终会被缩略化和截断。
mcp = FastMCP.from_openapi(
    openapi_spec=spec,
    client=client,
    mcp_names={
        "list_users__with_pagination": "user_list",
        "create_user__admin_required": "create_user", 
        "get_user_details__admin_required": "user_detail",
    }
)

在 mcp_names 中找不到的任何 operationId 都将使用默认策略（operationId 直到第一个 __）。

标签
版本
2.8.0
新增
FastMCP 提供了多种方式来向您的 MCP 组件添加标签，使您能够对它们进行分类和组织，以获得更好的可发现性和过滤。标签由多个来源组合，创建每个组件上的最终标签集。

RouteMap 标签
您可以使用 RouteMap 中的 mcp_tags 参数向从特定路由创建的组件添加自定义标签。这些标签将应用于从匹配该特定路由映射的路由创建的所有组件。
from fastmcp.server.openapi import RouteMap, MCPType

mcp = FastMCP.from_openapi(
    openapi_spec=spec,
    client=client,
    route_maps=[
        # 向所有 POST 端点添加自定义标签
        RouteMap(
            methods=["POST"],
            pattern=r".*",
            mcp_type=MCPType.TOOL,
            mcp_tags={"write-operation", "api-mutation"}
        ),
        
        # 向详细视图端点添加不同标签
        RouteMap(
            methods=["GET"],
            pattern=r".*\{.*\}.*",
            mcp_type=MCPType.RESOURCE_TEMPLATE,
            mcp_tags={"detail-view", "parameterized"}
        ),
        
        # 向列表端点添加标签
        RouteMap(
            methods=["GET"],
            pattern=r".*",
            mcp_type=MCPType.RESOURCE,
            mcp_tags={"list-data", "collection"}
        ),
    ],
)

全局标签
您可以在创建 MCP 服务器时提供 tags 参数来向所有组件添加标签。这些全局标签将应用于从 OpenAPI 规范创建的每个组件。
mcp = FastMCP.from_openapi(
    openapi_spec=spec,
    client=client,
    tags={"api-v2", "production", "external"}
)

客户端元数据中的 OpenAPI 标签
FastMCP 自动将您规范中的 OpenAPI 标签包含在组件的元数据中。这些标签通过 _meta._fastmcp.tags 字段对 MCP 客户端可用，允许客户端根据原始 OpenAPI 标记进行过滤和组织组件：
带有标签的 OpenAPI 规范
在 MCP 客户端中访问 OpenAPI 标签
{
  "paths": {
    "/users": {
      "get": {
        "tags": ["users", "public"],
        "operationId": "list_users",
        "summary": "列出所有用户"
      }
    }
  }
}

这使客户端能够轻松地根据原始 OpenAPI 分类理解和组织 API 端点。

高级自定义
版本
2.5.0
新增
默认情况下，FastMCP 使用 OpenAPI 规范中的各种元数据创建 MCP 组件，例如将 OpenAPI 描述合并到 MCP 组件描述中。
有时您可能希望以各种方式修改这些 MCP 组件，例如添加 LLM 特定指令或标签。对于精细的自定义，您可以在创建 MCP 服务器时提供 mcp_component_fn。在创建每个 MCP 组件后，此函数在其上被调用，并有机会就地修改它。
您的 mcp_component_fn 应该就地修改组件，而不是返回新组件。函数的结果被忽略。
from fastmcp.server.openapi import (
    HTTPRoute, 
    OpenAPITool, 
    OpenAPIResource, 
    OpenAPIResourceTemplate,
)

# 如果使用实验性解析器，请从实验模块导入：
# from fastmcp.experimental.server.openapi import (
#     HTTPRoute,
#     OpenAPITool,
#     OpenAPIResource,
#     OpenAPIResourceTemplate,
# )

def customize_components(
    route: HTTPRoute, 
    component: OpenAPITool | OpenAPIResource | OpenAPIResourceTemplate,
) -> None:
    # 向所有组件添加自定义标签
    component.tags.add("openapi")
    
    # 根据组件类型进行自定义
    if isinstance(component, OpenAPITool):
        component.description = f"🔧 {component.description} (via API)"
    
    if isinstance(component, OpenAPIResource):
        component.description = f"📊 {component.description}"
        component.tags.add("data")

mcp = FastMCP.from_openapi(
    openapi_spec=spec,
    client=client,
    mcp_component_fn=customize_components,
)

请求参数处理
FastMCP 智能地处理 OpenAPI 请求中的不同类型参数：

查询参数
默认情况下，FastMCP 仅包含具有非空值的查询参数。具有 None 值或空字符串的参数会被自动过滤。
# 调用此工具时...
await client.call_tool("search_products", {
    "category": "electronics",  # ✅ 包含
    "min_price": 100,           # ✅ 包含
    "max_price": None,          # ❌ 排除
    "brand": "",                # ❌ 排除
})

# HTTP 请求将是：GET /products?category=electronics&min_price=100

路径参数
路径参数通常是 REST API 所必需的。FastMCP：
过滤 None 值
验证提供了所有必需的路径参数
为缺少的必需参数引发清晰的错误
# ✅ 这有效
await client.call_tool("get_user", {"user_id": 123})

# ❌ 这会引发："缺少必需的路径参数：{'user_id'}"
await client.call_tool("get_user", {"user_id": None})

数组参数
FastMCP 根据 OpenAPI 规范处理数组参数：
查询数组：根据 explode 参数进行序列化（默认：True）
路径数组：序列化为逗号分隔值（OpenAPI ‘simple’ 样式）
# 带有 explode=true 的查询数组（默认）
# ?tags=red&tags=blue&tags=green

# 带有 explode=false 的查询数组
# ?tags=red,blue,green

# 路径数组（始终以逗号分隔）
# /items/red,blue,green

请求头
请求头参数会自动转换为字符串并包含在 HTTP 请求中。
FastAPI 🤝 FastMCP

ChatGPT 🤝 FastMCP

x

---

安装
创建服务端
内存内
Streamable HTTP
STDIO
MCP 配置
身份认证
AI SDK
Pydantic AI 🤝 FastMCP

使用 FastMCPToolset 将 FastMCP 服务端连接到 Pydantic AI agent

Pydantic AI 提供了 FastMCPToolset，让 Pydantic AI agent 可以通过 FastMCP Client 调用任意 MCP 服务端暴露的工具。由于该 toolset 构建在 FastMCP Client 之上，它既适用于 FastMCP 服务端，也适用于任何其他 MCP 服务端，并支持完整的传输：内存内、STDIO、Streamable HTTP 和 SSE。
本页展示如何让 FastMCPToolset 指向 FastMCP 服务端，并为每种传输提供示例。有关该 toolset 的完整 API，请参阅 Pydantic AI 文档。
FastMCPToolset 目前会向 agent 暴露工具。用户征询和采样等其他 MCP 特性尚不能通过此 toolset 使用；如需这些能力，请使用 Pydantic AI 标准的 MCPServer 客户端。

安装
FastMCPToolset 位于 pydantic-ai-slim 中的 fastmcp 可选组：
pip install "pydantic-ai-slim[fastmcp]"

创建服务端
创建一个包含你想暴露的工具的 FastMCP 服务端。本指南会一直使用一个简单的掷骰子工具。
server.py
import random
from fastmcp import FastMCP

mcp = FastMCP(name="Dice Roller")

@mcp.tool
def roll_dice(n_dice: int) -> list[int]:
    """掷 `n_dice` 个六面骰子并返回结果。"""
    return [random.randint(1, 6) for _ in range(n_dice)]

if __name__ == "__main__":
    mcp.run(transport="http", port=8000)

内存内
如果 FastMCP 服务端与 agent 位于同一进程中，请直接传入 FastMCP 实例。该 toolset 会复用内存内传输，避免网络往返，是测试和嵌入式使用中最快的选项。
import asyncio
import random
from fastmcp import FastMCP
from pydantic_ai import Agent
from pydantic_ai.toolsets.fastmcp import FastMCPToolset

mcp = FastMCP(name="Dice Roller")

@mcp.tool
def roll_dice(n_dice: int) -> list[int]:
    return [random.randint(1, 6) for _ in range(n_dice)]

toolset = FastMCPToolset(mcp)
agent = Agent("openai:gpt-4.1", toolsets=[toolset])

async def main():
    result = await agent.run("Roll 3 dice!")
    print(result.output)

if __name__ == "__main__":
    asyncio.run(main())

Streamable HTTP
对于可通过 HTTP 访问的远程 FastMCP 服务端，请将 URL 作为字符串传入。该 toolset 会从 URL 推断出 Streamable HTTP 传输。
from pydantic_ai import Agent
from pydantic_ai.toolsets.fastmcp import FastMCPToolset

toolset = FastMCPToolset("https://your-server-url.com/mcp")
agent = Agent("openai:gpt-4.1", toolsets=[toolset])

对于 SSE，请改用 /sse URL。

STDIO
要将 FastMCP 服务端作为子进程启动，请传入脚本路径，该 toolset 会使用 STDIO 传输。
from pydantic_ai import Agent
from pydantic_ai.toolsets.fastmcp import FastMCPToolset

toolset = FastMCPToolset("server.py")
agent = Agent("openai:gpt-4.1", toolsets=[toolset])

当你需要控制命令、参数或环境时，也可以直接传入 StdioTransport。

MCP 配置
要一次连接多个服务端，请传入 MCP 配置字典。该 toolset 会为每个服务端打开一个客户端，并将它们的所有工具暴露给 agent。
from pydantic_ai import Agent
from pydantic_ai.toolsets.fastmcp import FastMCPToolset

mcp_config = {
    "mcpServers": {
        "dice": {"command": "python", "args": ["server.py"]},
        "weather": {"url": "https://weather.example.com/mcp"},
    }
}

toolset = FastMCPToolset(mcp_config)
agent = Agent("openai:gpt-4.1", toolsets=[toolset])

身份认证
由于 FastMCPToolset 包装了 FastMCP Client，它继承了客户端完整的身份认证能力。要向远程服务端传递 bearer token 等凭据，请自行构建 Client（或 StreamableHttpTransport），再交给该 toolset。
from fastmcp import Client
from fastmcp.client.transports import StreamableHttpTransport
from pydantic_ai import Agent
from pydantic_ai.toolsets.fastmcp import FastMCPToolset

transport = StreamableHttpTransport(
    url="https://your-server-url.com/mcp",
    headers={"Authorization": "Bearer your-access-token"},
)

toolset = FastMCPToolset(Client(transport))
agent = Agent("openai:gpt-4.1", toolsets=[toolset])

对于 OAuth 流程，在构建 Client 时请使用 FastMCP 的 OAuth 辅助类。关于服务端侧令牌验证，请参阅 Token Verification。
OpenAI API 🤝 FastMCP

MCP JSON 配置文件 🤝 FastMCP

x

---
