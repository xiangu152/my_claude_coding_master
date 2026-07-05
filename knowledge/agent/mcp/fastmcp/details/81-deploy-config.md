---
title: "项目配置"
source: "https://fastmcp.wiki/zh/deployment/server-configuration"
version: "latest"
---

# 项目配置

> 原始文档来源：https://fastmcp.wiki/zh/deployment/server-configuration

---

概述
文件结构
JSON Schema 支持
源码配置
环境配置
部署配置
环境变量插值
与 CLI 命令的使用
预构建环境
使用现有环境
使用现有源码
CLI 覆盖行为
自定义命名模式
示例
从 CLI 参数迁移
部署
项目配置

使用 fastmcp.json 进行便携式、声明式的项目配置

版本
2.12.0
新增
FastMCP 通过 fastmcp.json 文件支持声明式配置。这是配置 FastMCP 项目的标准和首选方式，为服务器设置、依赖项和部署选项提供单一可信来源，替代复杂的命令行参数。
fastmcp.json 文件被设计为服务器配置的可移植描述，可以在环境和团队之间共享。从 fastmcp.json 文件运行时，您可以使用 CLI 参数覆盖任何配置值。

概述
fastmcp.json 配置文件允许您以结构化、可共享的格式定义 FastMCP 服务器的所有方面。无需记住命令行参数或编写 shell 脚本，您只需声明一次服务器配置，然后在任何地方使用。
当您有 fastmcp.json 文件时，运行服务器就变得如此简单：
# 使用配置运行服务器
fastmcp run fastmcp.json

# 或者如果 fastmcp.json 存在于当前目录中
fastmcp run

这种配置方法确保了从本地开发到生产服务器等不同环境之间的可重现部署。它与 Claude Desktop、VS Code 扩展以及任何兼容 MCP 的客户端无缝协作。

文件结构
fastmcp.json 配置回答了关于您的服务器的三个基本问题：
Source（源码） = 您的服务器代码位于何处？
Environment（环境） = 它需要什么环境设置？
Deployment（部署） = 服务器应该如何运行？
这个概念模型帮助您理解每个配置部分的目的，并有效地组织您的设置。配置文件直接映射到这三个关注点：
{
  "$schema": "https://gofastmcp.com/public/schemas/fastmcp.json/v1.json",
  "source": {
    // WHERE（何处）：您的服务器代码位置
    "type": "filesystem",  // 可选，默认为 "filesystem"
    "path": "server.py",
    "entrypoint": "mcp"
  },
  "environment": {
    // WHAT（什么）：环境设置和依赖项
    "type": "uv",  // 可选，默认为 "uv"
    "python": ">=3.10",
    "dependencies": ["pandas", "numpy"]
  },
  "deployment": {
    // HOW（如何）：运行时配置
    "transport": "stdio",
    "log_level": "INFO"
  }
}

只有 source 字段是必需的。environment 和 deployment 部分是可选的，在需要时提供额外的配置。

JSON Schema 支持
FastMCP 为 IDE 自动完成和验证提供 JSON schemas。在您的 fastmcp.json 中添加 schema 引用以获得增强的开发体验：
{
  "$schema": "https://gofastmcp.com/public/schemas/fastmcp.json/v1.json",
  "source": {
    "path": "server.py",
    "entrypoint": "mcp"
  }
}

提供两个 schema URL：
特定版本：https://gofastmcp.com/public/schemas/fastmcp.json/v1.json
最新版本：https://gofastmcp.com/public/schemas/fastmcp.json/latest.json
当指定 schema 时，VS Code 等现代 IDE 将自动提供自动完成建议、验证和内联文档。

源码配置
源码配置确定您的服务器代码位于何处。它告诉 FastMCP 如何查找和加载您的服务器，无论是本地 Python 文件、远程仓库还是托管在云端。此部分是必需的，构成了您配置的基础。
Source

source
object必填
确定服务器代码位置的服务器源码配置。

type
string默认值:"filesystem"
确定使用哪种实现的源码类型标识符。目前支持用于本地文件的 "filesystem"。未来版本将添加对 "git" 和 "cloud" 源码类型的支持。

显示 FileSystemSource

未来的源码类型
未来版本将支持其他源码类型：
Git 仓库（type: "git"）用于直接从版本控制加载服务器代码
FastMCP Cloud（type: "cloud"）用于具有自动扩展和管理的托管服务器

环境配置
环境配置确定您的服务器需要什么环境设置。它控制 Python 环境的构建时设置，确保您的服务器使用所需的确切 Python 版本和依赖项运行。此部分在不同系统之间创建隔离的、可重现的环境。
FastMCP 使用可扩展的环境系统，具有可由不同环境提供商实现的基础 Environment 类。目前，FastMCP 支持使用 uv 强大的依赖解析器进行 Python 环境管理的 UVEnvironment。
Environment

environment
object
可选的环境配置。指定时，FastMCP 使用适当的环境实现来设置您的服务器运行时。

type
string默认值:"uv"
确定使用哪种实现的环境类型标识符。目前支持由 uv 管理的 Python 环境的 "uv"。如果省略，默认为 "uv"。

显示 UVEnvironment

当提供环境配置时，FastMCP：
检测环境类型（如果未指定则默认为 "uv"）
使用适当的提供商创建隔离环境
安装指定的依赖项
在这个干净的环境中运行您的服务器
这种构建时设置确保您的服务器始终具有所需的依赖项，而不会污染您的系统 Python 或与其他项目冲突。
未来的环境类型
与源码类型类似，未来版本可能支持其他环境类型以满足不同的运行时需求，例如 Docker 容器或 Python 之外的特定于语言的环境。

部署配置
部署配置控制您的服务器如何运行。它定义运行时行为，包括网络设置、环境变量和执行上下文。这些设置决定了您的服务器在执行时如何操作，从传输协议到日志级别。
环境变量包含在此部分中，因为它们是影响服务器执行时行为的运行时配置，而不是如何构建其环境。部署配置在每次服务器启动时都会应用，控制其操作特性。
Deployment Fields

deployment
object
服务器的可选运行时配置。

显示 Deployment Fields

环境变量插值
部署配置中的 env 字段支持使用 ${VAR_NAME} 语法对环境变量进行运行时插值。这使得基于您的部署环境进行动态配置成为可能：
{
  "deployment": {
    "env": {
      "API_URL": "https://api.${ENVIRONMENT}.example.com",
      "DATABASE_URL": "postgres://${DB_USER}:${DB_PASS}@${DB_HOST}/myapp",
      "CACHE_KEY": "myapp_${ENVIRONMENT}_${VERSION}"
    }
  }
}

当服务器启动时，FastMCP 用您系统环境变量的值替换 ${ENVIRONMENT}、${DB_USER} 等。如果变量不存在，占位符将保持原样。
示例：如果您的系统有 ENVIRONMENT=production 和 DB_HOST=db.example.com：
// 配置
{
  "deployment": {
    "env": {
      "API_URL": "https://api.${ENVIRONMENT}.example.com",
      "DB_HOST": "${DB_HOST}"
    }
  }
}

// 运行时结果
{
  "API_URL": "https://api.production.example.com",
  "DB_HOST": "db.example.com"
}

此功能特别适用于：
在开发、测试和生产环境中部署相同的配置
将敏感值保留在配置文件之外
构建动态 URL 和连接字符串
创建特定于环境的前缀或后缀

与 CLI 命令的使用
FastMCP 自动检测并使用当前目录中专门命名为 fastmcp.json 的文件，使服务器执行简单且一致。具有 FastMCP 配置格式但名称不同的文件不会被自动检测，必须明确指定：
# 自动检测当前目录中的 fastmcp.json
cd my-project
fastmcp run  # 不需要参数！

# 或明确指定配置文件
fastmcp run prod.fastmcp.json

# 当已经在 uv 环境中时跳过环境设置
fastmcp run fastmcp.json --skip-env

# 当源码已经准备好时跳过源码准备
fastmcp run fastmcp.json --skip-source

# 跳过环境和源码准备
fastmcp run fastmcp.json --skip-env --skip-source

预构建环境
您可以使用 fastmcp project prepare 创建一个预安装了所有依赖项的持久 uv 项目：
# 创建持久环境
fastmcp project prepare fastmcp.json --output-dir ./env

# 使用预构建环境运行服务器
fastmcp run fastmcp.json --project ./env

这种模式将环境设置（慢）与服务器执行（快）分离，对部署场景很有用。

使用现有环境
默认情况下，FastMCP 根据您的配置使用 uv 创建隔离环境。当您已经有合适的 Python 环境时，使用 --skip-env 标志跳过环境创建：
fastmcp run fastmcp.json --skip-env

当您已经有环境时：
您在一个已安装所有依赖项的激活虚拟环境中
您在一个预装依赖项的 Docker 容器内
您在一个预构建环境的 CI/CD 管道中
您使用的是安装了所有必需包的系统级安装
您在一个 uv 管理的环境中（防止无限递归）
此标志告诉 FastMCP：“我已经安装了所有内容，只需运行服务器。“

使用现有源码
当使用需要准备的源码类型（未来支持 git 仓库或云源码）时，当您已经有可用的源码时，使用 --skip-source 标志：
fastmcp run fastmcp.json --skip-source

当您已经有源码时：
您之前已克隆了 git 仓库，无需重新获取
您有云托管服务器的缓存副本
您在 CI/CD 管道中，源码检出是一个单独的步骤
您在本地迭代已下载的代码
此标志告诉 FastMCP：“我已经有源码，跳过任何下载/克隆步骤。”
注意：对于文件系统源码（本地 Python 文件），此标志无效，因为它们不需要准备。
配置文件适用于所有 FastMCP 命令：
run - 在生产模式下启动服务器
dev - 使用 Inspector UI 启动以进行开发
inspect - 查看服务器功能和配置
install - 安装到 Claude Desktop、Cursor 或其他 MCP 客户端
当未提供文件参数时，FastMCP 在当前目录中搜索 fastmcp.json。这意味着您可以简单地导航到项目目录并运行 fastmcp run 来启动具有所有配置设置的服务器。

CLI 覆盖行为
命令行参数优先于配置文件值，允许在不修改文件的情况下进行临时调整：
# 配置指定端口 3000，CLI 覆盖为 8080
fastmcp run fastmcp.json --port 8080

# 配置指定 stdio，CLI 覆盖为 HTTP
fastmcp run fastmcp.json --transport http

# 添加配置中没有的额外依赖项
fastmcp run fastmcp.json --with requests --with httpx

这种优先级顺序支持：
快速测试不同设置
部署脚本中特定于环境的覆盖
使用增加的日志级别进行调试
临时配置更改

自定义命名模式
您可以为不同环境使用不同的配置文件：
fastmcp.json - 默认配置
dev.fastmcp.json - 开发设置
prod.fastmcp.json - 生产设置
test_fastmcp.json - 测试配置
任何名称中包含 “fastmcp.json” 的文件都被识别为配置文件。

示例
基础配置
开发配置
生产配置
数据科学服务器
多环境设置
简单服务器的最小配置：
{
  "$schema": "https://gofastmcp.com/public/schemas/fastmcp.json/v1.json",
  "source": {
    "path": "server.py",
    "entrypoint": "mcp"
  }
}

此配置明确指定服务器入口点（mcp），明确要使用哪个服务器实例或工厂函数。使用所有默认值：STDIO 传输、无特殊依赖项、标准日志记录。

从 CLI 参数迁移
如果您当前使用命令行参数或 shell 脚本，迁移到 fastmcp.json 可以简化您的工作流程。以下是常见 CLI 模式如何映射到配置：
CLI 命令：
uv run --with pandas --with requests \
  fastmcp run server.py \
  --transport http \
  --port 8000 \
  --log-level INFO

等效的 fastmcp.json：
{
  "$schema": "https://gofastmcp.com/public/schemas/fastmcp.json/v1.json",
  "source": {
    "path": "server.py",
    "entrypoint": "mcp"
  },
  "environment": {
    "dependencies": ["pandas", "requests"]
  },
  "deployment": {
    "transport": "http",
    "port": 8000,
    "log_level": "INFO"
  }
}

现在只需运行：
fastmcp run  # 自动查找并使用 fastmcp.json

配置文件方法提供更好的文档、更容易的共享以及跨不同环境的一致执行，同时在需要时保持覆盖设置的灵活性。
Prefect Horizon

测试你的 FastMCP 服务端

x

