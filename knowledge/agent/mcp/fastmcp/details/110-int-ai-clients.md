---
title: "集成: AI 客户端 (Claude, ChatGPT, Gemini, Goose)"
source: "https://fastmcp.wiki/zh/integrations/claude-desktop"
version: "latest"
---

# 集成: AI 客户端 (Claude, ChatGPT, Gemini, Goose)

> 原始文档来源：https://fastmcp.wiki/zh/integrations/claude-desktop (FastMCP 集成文档)

---

要求
创建服务器
安装服务器
FastMCP CLI
依赖
Python 版本和项目目录
环境变量
手动配置
依赖项
环境变量
远程服务器
身份验证
AI 助手
Claude Desktop 🤝 FastMCP

将 FastMCP 服务器连接到 Claude Desktop

此集成专注于通过 STDIO 传输运行本地 FastMCP 服务端文件。 对于使用 HTTP 或 SSE 传输运行的远程服务端，请使用客户端的原生配置；FastMCP 的集成主要用于简化带依赖和 uv 命令的复杂本地设置。
Claude Desktop 通过本地 STDIO 连接和远程服务器（beta）支持 MCP 服务器，允许您使用 FastMCP 服务器的自定义工具、资源和提示来扩展 Claude 的功能。
远程 MCP 服务器支持目前处于 beta 阶段，适用于 Claude Pro、Max、Team 和 Enterprise 计划的用户（截至 2025 年 6 月）。大多数用户仍然需要使用本地 STDIO 连接。
本指南专门介绍在 Claude Desktop 中使用 FastMCP 服务器。有关一般的 Claude Desktop MCP 设置和官方示例，请参阅官方 Claude Desktop 快速入门指南。

要求
Claude Desktop 传统上要求 MCP 服务器使用 STDIO 传输在本地运行，其中您的服务器通过标准输入/输出而不是 HTTP 与 Claude 通信。但是，某些计划的用户现在也可以访问远程服务器支持。
如果你没有远程服务器支持或不需要连接远程服务器, 你可以创建一个通过STDIO在本地运行的代理服务器，并将请求转发到远程的HTTP服务器。 请参见下方的 代理服务器 部分。

创建服务器
本指南中的示例将使用以下简单的掷骰子服务器，保存为 server.py。
server.py
import random
from fastmcp import FastMCP

mcp = FastMCP(name="骰子投掷器")

@mcp.tool
def roll_dice(n_dice: int) -> list[int]:
    """掷 `n_dice` 个六面骰子，并返回结果。"""
    return [random.randint(1, 6) for _ in range(n_dice)]

if __name__ == "__main__":
    mcp.run()

安装服务器

FastMCP CLI
版本
2.10.3
新增
在 Claude Desktop 中安装 FastMCP 服务器的最简单方法是使用 fastmcp install claude-desktop 命令。这会自动处理配置和依赖管理。
在版本 2.10.3 之前，可以通过运行 fastmcp install <path> 而不指定客户端来管理 Claude Desktop。
fastmcp install claude-desktop server.py

安装命令支持与 run 命令相同的 file.py:object 表示法。如果未指定对象，它将自动在您的文件中查找名为 mcp、server 或 app 的 FastMCP 服务器对象：
# 如果您的服务器对象名为 'mcp'，这些是等效的
fastmcp install claude-desktop server.py
fastmcp install claude-desktop server.py:mcp

# 如果您的服务器有不同的名称，请使用显式对象名
fastmcp install claude-desktop server.py:my_custom_server

安装后，完全重启 Claude Desktop。您应该在输入框的左下角看到一个锤子图标（🔨），表示 MCP 工具可用。

依赖
FastMCP 在 Claude Desktop 中安装时提供了几种管理服务器依赖的方法：
单个包：使用 --with 标志指定您的服务器需要的包。您可以多次使用此标志：
fastmcp install claude-desktop server.py --with pandas --with requests

要求文件：如果您有一个列出所有依赖的 requirements.txt 文件，请使用 --with-requirements 一次性安装它们：
fastmcp install claude-desktop server.py --with-requirements requirements.txt

可编辑包：对于正在开发的本地包，使用 --with-editable 以可编辑模式安装它们：
fastmcp install claude-desktop server.py --with-editable ./my-local-package

或者，您也可以使用一个名为 fastmcp.json 的配置文件（推荐使用）：
fastmcp.json
{
  "$schema": "https://gofastmcp.com/public/schemas/fastmcp.json/v1.json",
  "source": {
    "path": "server.py",
    "entrypoint": "mcp"
  },
  "environment": {
    "dependencies": ["pandas", "requests"]
  }
}

Python 版本和项目目录
FastMCP 允许您控制服务器的 Python 环境：
Python 版本：使用 --python 指定您的服务器应该使用的 Python 版本。当您的服务器需要特定 Python 版本时，这特别有用：
fastmcp install claude-desktop server.py --python 3.11

项目目录：使用 --project 在特定项目目录中运行您的服务器。这确保 uv 将从该项目中发现所有 pyproject.toml、uv.toml 和 .python-version 文件：
fastmcp install claude-desktop server.py --project /path/to/my-project

当您指定项目目录时，服务器中的所有相对路径将从该目录解析，并使用项目的虚拟环境。

环境变量
Claude Desktop 在完全隔离的环境中运行服务器，无法访问您的 shell 环境或本地安装的应用程序。您必须显式传递服务器需要的任何环境变量。
如果您的服务器需要环境变量（如 API 密钥），您必须包含它们：
fastmcp install claude-desktop server.py --server-name "Weather Server" \
  --env API_KEY=your-api-key \
  --env DEBUG=true

或者从 .env 文件中加载它们：
fastmcp install claude-desktop server.py --server-name "Weather Server" --env-file .env

必须在系统PATH中安装并可用uv. Claude Desktop 运行在其独立的隔离环境中，需要uv来管理依赖.
在macOS上，推荐使用Homebrew全局安装uv 这样Claude Desktop才能检测到它: brew install uv。 使用其他方式安装 uv 可能导致Claude Desktop无法访问它。

手动配置
为了更好地控制配置，您可以手动编辑 Claude Desktop 的配置文件。您可以从 Claude 的开发者设置中打开配置文件，或在以下位置找到它：
macOS: ~/Library/Application Support/Claude/claude_desktop_config.json
Windows: %APPDATA%\Claude\claude_desktop_config.json
配置文件是一个包含 mcpServers 键的 JSON 对象，其中包含每个 MCP 服务器的配置。
{
  "mcpServers": {
    "dice-roller": {
      "command": "python",
      "args": ["path/to/your/server.py"]
    }
  }
}

更新配置文件后，完全重启 Claude Desktop。查找锤子图标（🔨）以确认您的服务器已加载。

依赖项
如果你的服务器有依赖项，你可以使用 uv 或其他包管理器来配置环境。
手动配置依赖时，建议的方法是将 uv 与 FastMCP 一起使用。配置使用 uv run 来创建一个包含您指定包的隔离环境：
{
  "mcpServers": {
    "dice-roller": {
      "command": "uv",
      "args": [
        "run",
        "--with", "fastmcp",
        "--with", "pandas",
        "--with", "requests", 
        "fastmcp",
        "run",
        "path/to/your/server.py"
      ]
    }
  }
}

您也可以在配置中手动指定 Python 版本和项目目录。添加 --python 来使用特定的 Python 版本，或者 --project 在项目目录中运行：
{
  "mcpServers": {
    "dice-roller": {
      "command": "uv",
      "args": [
        "run",
        "--python", "3.11",
        "--project", "/path/to/project",
        "--with", "fastmcp",
        "fastmcp",
        "run",
        "path/to/your/server.py"
      ]
    }
  }
}

参数的顺序很重要：Python 版本和项目设置在包规格之前，而包规格在实际要运行的命令之前。
必须在系统PATH中安装并可用uv. Claude Desktop 运行在其独立的隔离环境中，需要uv来管理依赖.
在macOS上，推荐使用Homebrew全局安装uv 这样Claude Desktop才能检测到它: brew install uv。 使用其他方式安装 uv 可能导致Claude Desktop无法访问它。

环境变量
您也可以在配置中指定环境变量：
{
  "mcpServers": {
    "weather-server": {
      "command": "python",
      "args": ["path/to/weather_server.py"],
      "env": {
        "API_KEY": "your-api-key",
        "DEBUG": "true"
      }
    }
  }
}

Claude Desktop 在一个完全隔离的环境中运行服务器，无法访问你的 shell 环境或本地已安装的应用程序。你必须显式传递服务器所需的任何环境变量。

远程服务器
Claude Pro、Max、Team 和 Enterprise 计划的用户可以通过集成获得一流的远程服务器支持。对于其他用户，或作为替代方法，FastMCP 可以创建一个代理服务器，将请求转发到远程 HTTP 服务器。您可以在 Claude Desktop 中安装代理服务器。
创建一个连接到远程 HTTP 服务器的代理服务器：
proxy_server.py
from fastmcp import FastMCP

# 创建一个到远程服务器的代理
proxy = FastMCP.as_proxy(
    "https://example.com/mcp/sse", 
    name="Remote Server Proxy"
)

if __name__ == "__main__":
    proxy.run()  # 通过 STDIO 为 Claude Desktop 运行

身份验证
对于经过身份验证的远程服务器，请按照客户端身份验证文档中的指导创建经过身份验证的客户端，并将其传递给代理：
auth_proxy_server.py
from fastmcp import FastMCP, Client
from fastmcp.client.auth import BearerAuth

# 创建经过身份验证的客户端
client = Client(
    "https://api.example.com/mcp/sse",
    auth=BearerAuth(token="your-access-token")
)

# 使用经过身份验证的客户端创建代理
proxy = FastMCP.as_proxy(client, name="Authenticated Proxy")

if __name__ == "__main__":
    proxy.run()

Claude Code 🤝 FastMCP

Cursor 🤝 FastMCP

x

---

要求
创建服务器
安装服务器
FastMCP CLI
依赖
Python 版本和项目配置
环境变量
手动配置
使用服务器
AI 助手
Claude Code 🤝 FastMCP

在 Claude Code 中安装和使用 FastMCP 服务器

此集成专注于通过 STDIO 传输运行本地 FastMCP 服务端文件。 对于使用 HTTP 或 SSE 传输运行的远程服务端，请使用客户端的原生配置；FastMCP 的集成主要用于简化带依赖和 uv 命令的复杂本地设置。
Claude Code 通过包括 STDIO、SSE 和 HTTP 在内的多种传输方法支持 MCP 服务器，允许您使用 FastMCP 服务器的自定义工具、资源和提示来扩展 Claude 的功能。

要求
此集成使用 STDIO 传输在本地运行您的 FastMCP 服务器。对于远程部署，您可以使用 HTTP 或 SSE 传输运行您的 FastMCP 服务器，并使用 Claude Code 的内置 MCP 管理命令直接配置它。

创建服务器
本指南中的示例将使用以下简单的掷骰子服务器，保存为 server.py。
server.py
import random
from fastmcp import FastMCP

mcp = FastMCP(name="骰子投掷器")

@mcp.tool
def roll_dice(n_dice: int) -> list[int]:
    """掷 `n_dice` 个六面骰子，并返回结果。"""
    return [random.randint(1, 6) for _ in range(n_dice)]

if __name__ == "__main__":
    mcp.run()

安装服务器

FastMCP CLI
版本
2.10.3
新增
在 Claude Code 中安装 FastMCP 服务器的最简单方法是使用 fastmcp install claude-code 命令。这会自动处理配置、依赖管理，并调用 Claude Code 的内置 MCP 管理系统。
fastmcp install claude-code server.py

安装命令支持与 run 命令相同的 file.py:object 表示法。如果未指定对象，它将自动在您的文件中查找名为 mcp、server 或 app 的 FastMCP 服务器对象：
# 如果您的服务器对象名为“mcp”，那么这两者就是等同的。
fastmcp install claude-code server.py
fastmcp install claude-code server.py:mcp

# 如果您的服务器有别名，请使用明确的服务器名称。
fastmcp install claude-code server.py:my_custom_server

该命令将使用 Claude Code 的 claude mcp add 命令自动配置服务器。

依赖
FastMCP 为您的 Claude Code 服务器提供灵活的依赖管理选项：
单个包：使用 --with 标志指定您的服务器需要的包。您可以多次使用此标志：
fastmcp install claude-code server.py --with pandas --with requests

要求文件：如果您维护一个包含所有依赖的 requirements.txt 文件，请使用 --with-requirements 来安装它们：
fastmcp install claude-code server.py --with-requirements requirements.txt

可编辑包：对于正在开发的本地包，使用 --with-editable 以可编辑模式安装它们：
fastmcp install claude-code server.py --with-editable ./my-local-package

或者，您也可以使用一个名为 fastmcp.json 的配置文件（推荐使用）：
fastmcp.json
{
  "$schema": "https://gofastmcp.com/public/schemas/fastmcp.json/v1.json",
  "source": {
    "path": "server.py",
    "entrypoint": "mcp"
  },
  "environment": {
    "dependencies": ["pandas", "requests"]
  }
}

Python 版本和项目配置
使用这些选项控制您服务器的 Python 环境：
Python 版本：使用 --python 指定您的服务器需要的 Python 版本。当您的服务器需要特定的 Python 功能时，这可以确保兼容性：
fastmcp install claude-code server.py --python 3.11

项目目录：使用 --project 在特定的项目上下文中运行您的服务器。这告诉 uv 使用项目的配置文件和虚拟环境：
fastmcp install claude-code server.py --project /path/to/my-project

环境变量
如果您的服务器需要环境变量（如 API 密钥），您必须包含它们：
fastmcp install claude-code server.py --server-name "Weather Server" \
  --env API_KEY=your-api-key \
  --env DEBUG=true

或者从 .env 文件中加载它们：
fastmcp install claude-code server.py --server-name "Weather Server" --env-file .env

必须安装 Claude Code。集成在默认安装位置（~/.claude/local/claude）查找 Claude Code CLI，并使用 claude mcp add 命令来注册服务器。

手动配置
为了更好地控制配置，您可以手动使用 Claude Code 的内置 MCP 管理命令。这让您可以直接控制服务器的启动方式：
# 添加一台具有自定义配置的服务器
claude mcp add dice-roller -- uv run --with fastmcp fastmcp run server.py

# 使用环境变量添加
claude mcp add weather-server -e API_KEY=secret -e DEBUG=true -- uv run --with fastmcp fastmcp run server.py

# 附加具有特定范围（本地、用户或项目）的选项
claude mcp add my-server --scope user -- uv run --with fastmcp fastmcp run server.py

您也可以在 Claude Code 命令中手动指定 Python 版本和项目目录：
# 使用特定的 Python 版本
claude mcp add ml-server -- uv run --python 3.11 --with fastmcp fastmcp run server.py

# 在项目目录内
claude mcp add project-server -- uv run --project /path/to/project --with fastmcp fastmcp run server.py

使用服务器
一旦您的服务器安装完成，您就可以开始在 Claude Code 中使用您的 FastMCP 服务器。
尝试问 Claude 这样的问题：
“为我投几个骰子”
Claude 将自动检测您的 roll_dice 工具并使用它来满足您的请求，返回类似于以下内容：
我为您投一些骰子！这是您的结果：[4, 2, 6] 您投了三个骰子，得到了 4、2 和 6！
Claude Code 现在可以访问您在 FastMCP 服务器中定义的所有工具、资源和提示。
如果您的服务器提供资源，您可以使用 @server:protocol://resource/path 格式的 @ 提及来引用它们。如果您的服务器提供提示，您可以使用 /mcp__servername__promptname 作为旜线命令来使用它们。
ChatGPT 🤝 FastMCP

Claude Desktop 🤝 FastMCP

x

---

要求
创建服务器
安装服务器
FastMCP CLI
工作区安装
依赖
Python 版本和项目配置
环境变量
生成 MCP JSON
手动配置
依赖
环境变量
使用服务器
AI 助手
Cursor 🤝 FastMCP

在 Cursor 中安装和使用 FastMCP 服务器

此集成专注于通过 STDIO 传输运行本地 FastMCP 服务端文件。 对于使用 HTTP 或 SSE 传输运行的远程服务端，请使用客户端的原生配置；FastMCP 的集成主要用于简化带依赖和 uv 命令的复杂本地设置。
Cursor 通过包括 STDIO、SSE 和 Streamable HTTP 在内的多种传输方法支持 MCP 服务器，允许您使用 FastMCP 服务器的自定义工具、资源和提示来扩展 Cursor 的 AI 助手。

要求
此集成使用 STDIO 传输在本地运行您的 FastMCP 服务器。对于远程部署，您可以使用 HTTP 或 SSE 传输运行您的 FastMCP 服务器，并在 Cursor 的设置中直接配置它。

创建服务器
本指南中的示例将使用以下简单的掷骰子服务器，保存为 server.py。
server.py
import random
from fastmcp import FastMCP

mcp = FastMCP(name="骰子投掷器")

@mcp.tool
def roll_dice(n_dice: int) -> list[int]:
    """掷 `n_dice` 个六面骰子，并返回结果。"""
    return [random.randint(1, 6) for _ in range(n_dice)]

if __name__ == "__main__":
    mcp.run()

安装服务器

FastMCP CLI
版本
2.10.3
新增
在 Cursor 中安装 FastMCP 服务器的最简单方法是使用 fastmcp install cursor 命令。这会自动处理配置、依赖管理，并使用深度链接打开 Cursor 来安装服务器。
fastmcp install cursor server.py

工作区安装
版本
2.12.0
新增
默认情况下，FastMCP 为 Cursor 全局安装服务器。您也可以使用 --workspace 标志将服务器安装到项目特定的工作区：
# 安装到当前目录的 .cursor/ 文件夹
fastmcp install cursor server.py --workspace .

# 安装到特定工作区
fastmcp install cursor server.py --workspace /path/to/project

这会在指定的工作区目录中创建一个 .cursor/mcp.json 配置文件，允许不同的项目拥有自己的 MCP 服务器配置。
安装命令支持与 run 命令相同的 file.py:object 表示法。如果未指定对象，它将自动在您的文件中查找名为 mcp、server 或 app 的 FastMCP 服务器对象：
# 如果您的服务器对象名为 'mcp'，这些是等效的
fastmcp install cursor server.py
fastmcp install cursor server.py:mcp

# 如果您的服务器有不同的名称，请使用显式对象名
fastmcp install cursor server.py:my_custom_server

运行命令后，Cursor 将自动打开并提示您安装服务器。命令将是 uv，这是预期的，因为这是一个 Python STDIO 服务器。点击“安装”确认：

依赖
FastMCP 为您的 Cursor 服务器提供多种管理依赖的方式：
单个包：使用 --with 标志指定您的服务器需要的包。您可以多次使用此标志：
fastmcp install cursor server.py --with pandas --with requests

要求文件：对于有 requirements.txt 文件的项目，使用 --with-requirements 一次安装所有依赖：
fastmcp install cursor server.py --with-requirements requirements.txt

可编辑包：开发本地包时，使用 --with-editable 以可编辑模式安装它们：
fastmcp install cursor server.py --with-editable ./my-local-package

或者，您也可以使用一个名为 fastmcp.json 的配置文件（推荐使用）：
fastmcp.json
{
  "$schema": "https://gofastmcp.com/public/schemas/fastmcp.json/v1.json",
  "source": {
    "path": "server.py",
    "entrypoint": "mcp"
  },
  "environment": {
    "dependencies": ["pandas", "requests"]
  }
}

Python 版本和项目配置
使用这些选项控制服务器的 Python 环境：
Python 版本：使用 --python 指定您的服务器应该使用的 Python 版本。当您的服务器需要特定 Python 功能时，这是必需的：
fastmcp install cursor server.py --python 3.11

项目目录：使用 --project 在特定项目上下文中运行您的服务器。这确保 uv 发现所有项目配置文件并使用正确的虚拟环境：
fastmcp install cursor server.py --project /path/to/my-project

环境变量
Cursor 在完全隔离的环境中运行服务器，无法访问您的 shell 环境或本地安装的应用程序。您必须显式传递服务器需要的任何环境变量。
如果您的服务器需要环境变量（如 API 密钥），您必须包含它们：
fastmcp install cursor server.py --server-name "Weather Server" \
  --env API_KEY=your-api-key \
  --env DEBUG=true

或者从 .env 文件中加载它们：
fastmcp install cursor server.py --server-name "Weather Server" --env-file .env

必须安装 uv 并在系统 PATH 中可用。Cursor 在其自己的隔离环境中运行，需要 uv 来管理依赖。

生成 MCP JSON
使用上面的一流集成以获得最佳体验。 MCP JSON 生成对于高级用例、手动配置或与其他工具集成很有用。
您可以生成 MCP JSON 配置以供手动使用：
# 生成配置并输出到标准输出
fastmcp install mcp-json server.py --server-name "Dice Roller" --with pandas

# 将配置复制到剪贴板以便轻松粘贴
fastmcp install mcp-json server.py --server-name "Dice Roller" --copy

这会生成标准的 mcpServers 配置格式，可以与任何兼容 MCP 的客户端一起使用。

手动配置
为了更好地控制配置，您可以手动编辑 Cursor 的配置文件。配置文件位于：
所有平台：~/.cursor/mcp.json
配置文件是一个包含 mcpServers 键的 JSON 对象，其中包含每个 MCP 服务器的配置。
{
  "mcpServers": {
    "dice-roller": {
      "command": "python",
      "args": ["path/to/your/server.py"]
    }
  }
}

更新配置文件后，您的服务器应该在 Cursor 中可用。

依赖
如果您的服务器有依赖，您可以使用 uv 或其他包管理器来设置环境。
手动配置依赖时，建议的方法是将 uv 与 FastMCP 一起使用。配置应该使用 uv run 来创建一个包含您指定包的隔离环境：
{
  "mcpServers": {
    "dice-roller": {
      "command": "uv",
      "args": [
        "run",
        "--with", "fastmcp",
        "--with", "pandas",
        "--with", "requests", 
        "fastmcp",
        "run",
        "path/to/your/server.py"
      ]
    }
  }
}

您也可以在配置中手动指定 Python 版本和项目目录：
{
  "mcpServers": {
    "dice-roller": {
      "command": "uv",
      "args": [
        "run",
        "--python", "3.11",
        "--project", "/path/to/project",
        "--with", "fastmcp",
        "fastmcp",
        "run",
        "path/to/your/server.py"
      ]
    }
  }
}

请注意，参数的顺序很重要：Python 版本和项目设置应该在包规格之前。
必须安装 uv 并在系统 PATH 中可用。Cursor 在其自己的隔离环境中运行，需要 uv 来管理依赖。

环境变量
您也可以在配置中指定环境变量：
{
  "mcpServers": {
    "weather-server": {
      "command": "python",
      "args": ["path/to/weather_server.py"],
      "env": {
        "API_KEY": "your-api-key",
        "DEBUG": "true"
      }
    }
  }
}

Cursor 在完全隔离的环境中运行服务器，无法访问您的 shell 环境或本地安装的应用程序。您必须显式传递服务器需要的任何环境变量。

使用服务器
一旦您的服务器安装完成，您就可以开始在 Cursor 的 AI 助手中使用您的 FastMCP 服务器。
尝试问 Cursor 这样的问题：
“为我投几个骰子”
Cursor 将自动检测您的 roll_dice 工具并使用它来满足您的请求，返回类伺于以下内容：
🎲 这是您的骰子投放结果：4、6、4 您投了 3 个骰子，总共 14 点！那个 6 点是个不错的高分！
AI 助手现在可以访问您在 FastMCP 服务器中定义的所有工具、资源和提示。
Claude Desktop 🤝 FastMCP

Gemini CLI 🤝 FastMCP

x

---

构建服务端
部署你的服务端
Chat Mode
添加到 ChatGPT
1. 启用 Developer Mode
2. 创建 Connector
3. 在 Chat 中使用
跳过确认
Deep Research Mode
工具实现
使用 Deep Research
AI 助手
ChatGPT 🤝 FastMCP

在 Chat 和 Deep Research 模式中将 FastMCP 服务端连接到 ChatGPT

ChatGPT 支持通过远程 HTTP 连接使用 MCP 服务端，并提供两种模式：用于交互式对话的 Chat mode，以及用于全面信息检索的 Deep Research mode。
Chat Mode 需要 Developer Mode：要在常规 ChatGPT 对话中使用 MCP 服务端，必须先在 ChatGPT 设置中启用 Developer Mode。此功能面向 ChatGPT Pro、Team、Enterprise 和 Edu 用户开放。
OpenAI 官方 MCP 文档和示例使用 FastMCP v2 构建！可从其 MCP 文档和 Developer Mode 指南了解更多。

构建服务端
首先，创建一个简单的 FastMCP 服务端：
server.py
from fastmcp import FastMCP
import random

mcp = FastMCP("Demo Server")

@mcp.tool
def roll_dice(sides: int = 6) -> int:
    """掷一个指定面数的骰子。"""
    return random.randint(1, sides)

if __name__ == "__main__":
    mcp.run(transport="http", port=8000)

部署你的服务端
你的服务端必须能从互联网访问。开发时可以使用 ngrok：
Terminal 1
Terminal 2
python server.py

记下你的公共 URL（例如 https://abc123.ngrok.io），供后续步骤使用。

Chat Mode
Chat mode 允许你直接在 ChatGPT 对话中使用 MCP 工具。最新要求请参阅 OpenAI 的 Developer Mode 指南。

添加到 ChatGPT

1. 启用 Developer Mode
打开 ChatGPT，并进入 Settings → Connectors
在 Advanced 下启用 Developer Mode

2. 创建 Connector
在 Settings → Connectors 中点击 Create
输入：
Name：你的服务端名称
Server URL：https://your-server.ngrok.io/mcp/
勾选 I trust this provider
如有需要，添加身份认证
点击 Create
没有 Developer Mode 时：如果没有 search/fetch 工具，ChatGPT 会拒绝该服务端。启用 Developer Mode 后，Chat mode 不需要 search/fetch 工具。

3. 在 Chat 中使用
开始一个新聊天
点击 + 按钮 → More → Developer Mode
启用你的 MCP 服务端 connector（必需，每个聊天中都必须显式添加该 connector）
现在你可以使用工具了：
示例用法：
“Roll a 20-sided dice”
“Roll dice” (uses default 6 sides)
必须在每个聊天会话中通过 Developer Mode 显式启用该 connector。添加后，它会在整个对话期间保持激活。

跳过确认
对只读工具使用 annotations=ToolAnnotations(readOnlyHint=True)，可跳过确认提示：
from mcp.types import ToolAnnotations

@mcp.tool(annotations=ToolAnnotations(readOnlyHint=True))
def get_status() -> str:
    """检查系统状态。"""
    return "All systems operational"

@mcp.tool()  # 无 annotation，ChatGPT 可能会要求确认
def delete_item(id: str) -> str:
    """删除一个项目。"""
    return f"Deleted {id}"

Deep Research Mode
Deep Research mode 提供带引用的系统性信息检索。最新 Deep Research 规范请参阅 OpenAI 的 MCP 文档。
需要 Search 和 Fetch：没有 Developer Mode 时，ChatGPT 会拒绝任何不同时具备 search 和 fetch 工具的服务端。即使在 Developer Mode 中，Deep Research 也只使用这两个工具。

工具实现
Deep Research 工具必须遵循此模式：
@mcp.tool()
def search(query: str) -> dict:
    """
    搜索与查询匹配的记录。
    必须返回 {"ids": [字符串 ID 列表]}
    """
    # 你的搜索逻辑
    matching_ids = ["id1", "id2", "id3"]
    return {"ids": matching_ids}

@mcp.tool()
def fetch(id: str) -> dict:
    """
    按 ID 获取完整记录。
    返回完整记录数据供 ChatGPT 分析。
    """
    # 你的获取逻辑
    return {
        "id": id,
        "title": "Record Title",
        "content": "Full record content...",
        "metadata": {"author": "Jane Doe", "date": "2024"}
    }

使用 Deep Research
确保你的服务端已添加到 ChatGPT connectors（与 Chat mode 相同）
开始一个新聊天
点击 + → Deep Research
选择你的 MCP 服务端作为来源
提出研究问题
ChatGPT 会使用你的 search 和 fetch 工具查找并引用相关信息。
OpenAPI 🤝 FastMCP

Claude Code 🤝 FastMCP

x

---

Gemini Python SDK
创建服务端
调用服务端
远程和已认证服务端
AI SDK
Gemini SDK 🤝 FastMCP

将 FastMCP 服务端连接到 Google Gemini SDK

Google 的 Gemini API 在其 Python 和 JavaScript SDK 中内置了对 MCP 服务端的支持，让你可以直接连接到 MCP 服务端，并与 Gemini 模型无缝使用其工具。

Gemini Python SDK
Google 的 Gemini Python SDK 可以直接使用 FastMCP 客户端。
Google 的 MCP 集成目前处于实验阶段，可在 Python 和 JavaScript SDK 中使用。该 API 会在需要时自动调用 MCP 工具，并且可以连接本地和远程 MCP 服务端。
目前，Gemini 的 MCP 支持只能访问 MCP 服务端中的工具：它会查询 list_tools 端点，并将这些函数暴露给 AI。资源和提示词等其他 MCP 功能目前尚不支持。

创建服务端
首先，创建一个包含你想暴露工具的 FastMCP 服务端。在本示例中，我们会创建一个只有单个掷骰子工具的服务端。
server.py
import random
from fastmcp import FastMCP

mcp = FastMCP(name="Dice Roller")

@mcp.tool
def roll_dice(n_dice: int) -> list[int]:
    """掷 `n_dice` 个 6 面骰子并返回结果。"""
    return [random.randint(1, 6) for _ in range(n_dice)]

if __name__ == "__main__":
    mcp.run()

调用服务端
要将 Gemini API 与 MCP 一起使用，你需要安装 Google Generative AI SDK：
pip install google-genai

你还需要向 Google 进行身份认证。可以通过设置 GEMINI_API_KEY 环境变量来完成。更多信息请参阅 Gemini SDK 文档。
export GEMINI_API_KEY="your-api-key"

Gemini SDK 会直接与 MCP 客户端会话交互。要调用服务端，你需要实例化 FastMCP 客户端，进入其连接上下文，并将客户端会话传给 Gemini SDK。
from fastmcp import Client
from google import genai
import asyncio

mcp_client = Client("server.py")
gemini_client = genai.Client()

async def main():    
    async with mcp_client:
        response = await gemini_client.aio.models.generate_content(
            model="gemini-2.0-flash",
            contents="Roll 3 dice!",
            config=genai.types.GenerateContentConfig(
                temperature=0,
                tools=[mcp_client.session],  # 传入 FastMCP 客户端会话
            ),
        )
        print(response.text)

if __name__ == "__main__":
    asyncio.run(main())

运行这段代码后，你会看到类似输出：
好的，我掷了 3 个骰子，得到了 5、4 和 1。

远程和已认证服务端
在上面的示例中，我们使用 stdio 传输连接到了本地服务端。因为使用的是 FastMCP 客户端，所以只需更改客户端配置，你也可以使用 FastMCP 支持的任何传输或认证方法，连接到任意本地或远程 MCP 服务端。
例如，要连接到远程且需要认证的服务端，可以使用以下客户端：
from fastmcp import Client
from fastmcp.client.auth import BearerAuth

mcp_client = Client(
    "https://my-server.com/mcp/",
    auth=BearerAuth("<your-token>"),
)

其余代码保持不变。
Anthropic API 🤝 FastMCP

OpenAI API 🤝 FastMCP

x

---

要求
创建服务端
安装服务端
FastMCP CLI
依赖
Python 版本和项目配置
环境变量
手动配置
使用服务端
AI 助手
Gemini CLI 🤝 FastMCP

在 Gemini CLI 中安装并使用 FastMCP 服务端

此集成专注于通过 STDIO 传输运行本地 FastMCP 服务端文件。 对于使用 HTTP 或 SSE 传输运行的远程服务端，请使用客户端的原生配置；FastMCP 的集成主要用于简化带依赖和 uv 命令的复杂本地设置。
Gemini CLI 支持通过 STDIO、SSE 和 HTTP 等多种传输方式使用 MCP 服务端，让你可以通过 FastMCP 服务端中的自定义工具、资源和提示词扩展 Gemini 的能力。

要求
此集成使用 STDIO 传输在本地运行 FastMCP 服务端。对于远程部署，可以使用 HTTP 或 SSE 传输运行 FastMCP 服务端，并通过 Gemini CLI 内置的 MCP 管理命令直接配置。

创建服务端
本指南中的示例会使用下面这个简单的掷骰子服务端，并将其保存为 server.py。
server.py
import random
from fastmcp import FastMCP

mcp = FastMCP(name="Dice Roller")

@mcp.tool
def roll_dice(n_dice: int) -> list[int]:
    """掷 `n_dice` 个六面骰子并返回结果。"""
    return [random.randint(1, 6) for _ in range(n_dice)]

if __name__ == "__main__":
    mcp.run()

安装服务端

FastMCP CLI
版本
2.13.0
新增
在 Gemini CLI 中安装 FastMCP 服务端最简单的方式是使用 fastmcp install gemini-cli 命令。它会自动处理配置和依赖管理，并调用 Gemini CLI 内置的 MCP 管理系统。
fastmcp install gemini-cli server.py

安装命令支持与 run 命令相同的 file.py:object 表示法。如果未指定对象，它会在文件中自动查找名为 mcp、server 或 app 的 FastMCP 服务端对象：
# 如果你的服务端对象名为 'mcp'，这两条命令等价
fastmcp install gemini-cli server.py
fastmcp install gemini-cli server.py:mcp

# 如果服务端对象名称不同，请显式指定对象名
fastmcp install gemini-cli server.py:my_custom_server

该命令会使用 Gemini CLI 的 gemini mcp add 命令自动配置服务端。

依赖
FastMCP 为 Gemini CLI 服务端提供灵活的依赖管理选项：
单独包：使用 --with 标志指定服务端需要的包。你可以多次使用该标志：
fastmcp install gemini-cli server.py --with pandas --with requests

Requirements 文件：如果你使用 requirements.txt 文件维护所有依赖，请使用 --with-requirements 安装它们：
fastmcp install gemini-cli server.py --with-requirements requirements.txt

可编辑包：对于正在开发的本地包，请使用 --with-editable 以可编辑模式安装：
fastmcp install gemini-cli server.py --with-editable ./my-local-package

或者，你可以使用 fastmcp.json 配置文件（推荐）：
fastmcp.json
{
  "$schema": "https://gofastmcp.com/public/schemas/fastmcp.json/v1.json",
  "source": {
    "path": "server.py",
    "entrypoint": "mcp"
  },
  "environment": {
    "dependencies": ["pandas", "requests"]
  }
}

Python 版本和项目配置
使用这些选项控制服务端的 Python 环境：
Python 版本：使用 --python 指定服务端所需的 Python 版本。当服务端需要特定 Python 特性时，这可以确保兼容性：
fastmcp install gemini-cli server.py --python 3.11

项目目录：使用 --project 在特定项目上下文中运行服务端。这会告诉 uv 使用该项目的配置文件和虚拟环境：
fastmcp install gemini-cli server.py --project /path/to/my-project

环境变量
如果服务端需要环境变量（例如 API key），必须包含它们：
fastmcp install gemini-cli server.py --server-name "Weather Server" \
  --env API_KEY=your-api-key \
  --env DEBUG=true

或从 .env 文件加载：
fastmcp install gemini-cli server.py --server-name "Weather Server" --env-file .env

必须安装 Gemini CLI。该集成会查找 Gemini CLI，并使用 gemini mcp add 命令注册服务端。

手动配置
如需更精细地控制配置，可以手动使用 Gemini CLI 内置的 MCP 管理命令。这让你可以直接控制服务端如何启动：
# 添加带自定义配置的服务端
gemini mcp add dice-roller uv -- run --with fastmcp fastmcp run server.py

# 添加并携带环境变量
gemini mcp add weather-server -e API_KEY=secret -e DEBUG=true uv -- run --with fastmcp fastmcp run server.py

# 添加并指定 scope（user 或 project）
gemini mcp add my-server --scope user uv -- run --with fastmcp fastmcp run server.py

你也可以在 Gemini CLI 命令中手动指定 Python 版本和项目目录：
# 使用特定 Python 版本
gemini mcp add ml-server uv -- run --python 3.11 --with fastmcp fastmcp run server.py

# 在项目目录中
gemini mcp add project-server uv -- run --project /path/to/project --with fastmcp fastmcp run server.py

使用服务端
服务端安装完成后，你就可以通过 Gemini CLI 开始使用 FastMCP 服务端。
试着向 Gemini 提出类似下面的问题：
“Roll some dice for me”
Gemini 会自动检测你的 roll_dice 工具，并使用它完成请求。
Gemini CLI 现在可以访问你在 FastMCP 服务端中定义的所有工具和提示词。
如果服务端提供提示词，你可以通过 /prompt_name 将它们作为斜杠命令使用。
Cursor 🤝 FastMCP

Goose 🤝 FastMCP

x

---

要求
创建服务端
安装服务端
FastMCP CLI
依赖
Python 版本
环境变量
手动配置
依赖
环境变量
使用服务端
AI 助手
Goose 🤝 FastMCP

在 Goose 中安装并使用 FastMCP 服务端

此集成专注于通过 STDIO 传输运行本地 FastMCP 服务端文件。 对于使用 HTTP 或 SSE 传输运行的远程服务端，请使用客户端的原生配置；FastMCP 的集成主要用于简化带依赖和 uv 命令的复杂本地设置。
Goose 是 Block 推出的开源 AI agent，支持将 MCP 服务端作为扩展使用。FastMCP 可以通过 Goose 的 deeplink 协议将你的服务端直接安装到 Goose 中，一个命令就能打开 Goose 并显示可继续操作的安装对话框。

要求
此集成使用 Goose 的 deeplink 协议，将你的服务端注册为通过 uvx 运行的 STDIO 扩展。要让 deeplink 自动打开，系统中必须已安装 Goose。
对于远程部署，请为 FastMCP 服务端配置 HTTP 传输，并使用 goose configure 或配置文件将其直接添加到 Goose。

创建服务端
本指南中的示例会使用下面这个简单的掷骰子服务端，并将其保存为 server.py。
server.py
import random
from fastmcp import FastMCP

mcp = FastMCP(name="Dice Roller")

@mcp.tool
def roll_dice(n_dice: int) -> list[int]:
    """掷 `n_dice` 个六面骰子并返回结果。"""
    return [random.randint(1, 6) for _ in range(n_dice)]

if __name__ == "__main__":
    mcp.run()

安装服务端

FastMCP CLI
版本
3.0.0
新增
在 Goose 中安装 FastMCP 服务端最简单的方式是使用 fastmcp install goose 命令。它会生成一个 goose:// deeplink 并打开它，提示 Goose 安装该服务端。
fastmcp install goose server.py

安装命令支持与 run 命令相同的 file.py:object 表示法。如果未指定对象，它会在文件中自动查找名为 mcp、server 或 app 的 FastMCP 服务端对象：
# 如果你的服务端对象名为 'mcp'，这两条命令等价
fastmcp install goose server.py
fastmcp install goose server.py:mcp

# 如果服务端对象名称不同，请显式指定对象名
fastmcp install goose server.py:my_custom_server

在底层，生成的命令会使用 uvx 在隔离环境中运行你的服务端。Goose 要求使用 uvx 而不是 uv run，因此安装会生成类似这样的命令：
uvx --with pandas fastmcp run /path/to/server.py

依赖
使用 --with 标志指定服务端需要的额外包：
fastmcp install goose server.py --with pandas --with requests

或者，你可以使用 fastmcp.json 配置文件（推荐）：
fastmcp.json
{
  "$schema": "https://gofastmcp.com/public/schemas/fastmcp.json/v1.json",
  "source": {
    "path": "server.py",
    "entrypoint": "mcp"
  },
  "environment": {
    "dependencies": ["pandas", "requests"]
  }
}

Python 版本
使用 --python 指定服务端应使用的 Python 版本：
fastmcp install goose server.py --python 3.11

Goose 安装使用 uvx，它不支持 --project、--with-requirements 或 --with-editable。如果你需要这些选项，请使用 fastmcp install mcp-json 生成完整配置，然后手动添加到 Goose。

环境变量
Goose 的 deeplink 协议不支持环境变量。如果你的服务端需要环境变量（例如 API key），你有两个选择：
安装后配置：运行 goose configure，并将环境变量添加到扩展中。
手动配置：使用 fastmcp install mcp-json 生成完整配置，然后通过 envs 字段将其添加到 ~/.config/goose/config.yaml。

手动配置
如需更多控制，可以手动编辑 Goose 位于 ~/.config/goose/config.yaml 的配置文件：
extensions:
  dice-roller:
    name: Dice Roller
    cmd: uvx
    args: [fastmcp, run, /path/to/server.py]
    enabled: true
    type: stdio
    timeout: 300

依赖
手动配置时，请在 args 中使用 --with 标志添加包：
extensions:
  dice-roller:
    name: Dice Roller
    cmd: uvx
    args: [--with, pandas, --with, requests, fastmcp, run, /path/to/server.py]
    enabled: true
    type: stdio
    timeout: 300

环境变量
可以在 envs 字段中指定环境变量：
extensions:
  weather-server:
    name: Weather Server
    cmd: uvx
    args: [fastmcp, run, /path/to/weather_server.py]
    enabled: true
    envs:
      API_KEY: your-api-key
      DEBUG: "true"
    type: stdio
    timeout: 300

你也可以使用 goose configure 以交互方式添加扩展，它会提示输入环境变量。
必须安装 uvx（来自 uv），并确保它在系统 PATH 中可用。Goose 使用 uvx 在隔离环境中运行基于 Python 的扩展。

使用服务端
服务端安装完成后，你就可以在 Goose 中开始使用 FastMCP 服务端。
试着向 Goose 提出类似下面的问题：
“Roll some dice for me”
Goose 会自动检测你的 roll_dice 工具，并使用它完成请求，返回类似下面的结果：
🎲 Here are your dice rolls: 4, 6, 4 You rolled 3 dice with a total of 14!
Goose 现在可以访问你在 FastMCP 服务端中定义的所有工具、资源和提示词。
Gemini CLI 🤝 FastMCP

Anthropic API 🤝 FastMCP

x

---
