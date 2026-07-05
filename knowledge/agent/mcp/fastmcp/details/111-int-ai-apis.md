---
title: "集成: AI APIs (OpenAI, Anthropic)"
source: "https://fastmcp.wiki/zh/integrations/openai"
version: "latest"
---

# 集成: AI APIs (OpenAI, Anthropic)

> 原始文档来源：https://fastmcp.wiki/zh/integrations/openai (FastMCP 集成文档)

---

Responses API
创建服务器
部署服务器
调用服务器
身份验证
服务器身份验证
客户端身份验证
AI SDK
OpenAI API 🤝 FastMCP

将 FastMCP 服务器与 OpenAI API 连接

Responses API
OpenAI 的 Responses API 支持 MCP 服务器作为远程工具源，允许您使用自定义函数扩展 AI 功能。
Responses API 是与 OpenAI 的 Completions API 或 Assistants API 不同的独立 API。目前，只有 Responses API 支持 MCP。
目前，Responses API 仅访问 MCP 服务器的工具——它查询 list_tools 端点并将这些功能暴露给 AI 代理。目前不支持其他 MCP 功能，如资源和提示。

创建服务器
首先，创建一个包含您要暴露的工具的 FastMCP 服务器。在此示例中，我们将创建一个带有掷骰子单一工具的服务器。
server.py
import random
from fastmcp import FastMCP

mcp = FastMCP(name="骰子投掷器")

@mcp.tool
def roll_dice(n_dice: int) -> list[int]:
    """掷 `n_dice` 个六面骰子并返回结果。"""
    return [random.randint(1, 6) for _ in range(n_dice)]

if __name__ == "__main__":
    mcp.run(transport="http", port=8000)

部署服务器
您的服务器必须部署到公共 URL，以便 OpenAI 能够访问它。
对于开发，您可以使用像 ngrok 这样的工具临时将本地运行的服务器暴露到互联网。我们将在此示例中这样做（您可能需要安装 ngrok 并创建一个免费账户），但您可以使用任何其他方法来部署您的服务器。
假设您将上述代码保存为 server.py，您可以在两个独立的终端中运行以下两个命令来部署您的服务器并将其暴露到互联网：
FastMCP 服务器
ngrok
python server.py

这会将您的未经身份验证的服务器暴露到互联网。只有在您了解风险的情况下，才在安全环境中运行此命令。

调用服务器
要使用 Responses API，您需要安装 OpenAI Python SDK（FastMCP 中不包含）：
pip install openai

您还需要通过 OpenAI 进行身份验证。您可以通过设置 OPENAI_API_KEY 环境变量来完成此操作。有关更多信息，请查阅 OpenAI SDK 文档。
export OPENAI_API_KEY="your-api-key"

以下是如何从 Python 调用您的服务器的示例。请注意，您需要将 https://your-server-url.com 替换为您服务器的实际 URL。此外，我们使用 /mcp/ 作为端点，因为我们部署了一个使用默认路径的可流式 HTTP 服务器；如果您自定义了服务器的部署，可能需要使用不同的端点。
from openai import OpenAI

# 您的服务器 URL（替换为您的实际 URL）
url = 'https://your-server-url.com'

client = OpenAI()

resp = client.responses.create(
    model="gpt-4.1",
    tools=[
        {
            "type": "mcp",
            "server_label": "dice_server",
            "server_url": f"{url}/mcp/",
            "require_approval": "never",
        },
    ],
    input="投几个骰子！",
)

print(resp.output_text)

如果您运行此代码，您将看到类似以下的输出：
您投了 3 个骰子，得到以下结果：6、4 和 2！

身份验证
版本
2.6.0
新增
Responses API 可以包含用于身份验证请求的请求头，这意味着您不必担心您的服务器被公开访问。

服务器身份验证
向服务器添加身份验证的最简单方法是使用承载令牌方案。
在此示例中，我们将使用 FastMCP 的 RSAKeyPair 工具快速生成我们自己的令牌，但这可能不适用于生产使用。有关更多详细信息，请参阅完整的服务器端令牌验证文档。
我们将首先创建一个 RSA 密钥对来签名和验证令牌。
from fastmcp.server.auth.providers.jwt import RSAKeyPair

key_pair = RSAKeyPair.generate()
access_token = key_pair.create_token(audience="dice-server")

FastMCP 的 RSAKeyPair 工具仅用于开发和测试。
接下来，我们将创建一个 JWTVerifier 来验证服务器。
from fastmcp import FastMCP
from fastmcp.server.auth import JWTVerifier

auth = JWTVerifier(
    public_key=key_pair.public_key,
    audience="dice-server",
)

mcp = FastMCP(name="Dice Roller", auth=auth)

以下是一个您可以复制/粘贴的完整示例。为了简单起见并仅为此示例目的，它将把令牌打印到控制台。在生产中请勿这样做！
server.py
from fastmcp import FastMCP
from fastmcp.server.auth import JWTVerifier
from fastmcp.server.auth.providers.jwt import RSAKeyPair
import random

key_pair = RSAKeyPair.generate()
access_token = key_pair.create_token(audience="dice-server")

auth = JWTVerifier(
    public_key=key_pair.public_key,
    audience="dice-server",
)

mcp = FastMCP(name="Dice Roller", auth=auth)

@mcp.tool
def roll_dice(n_dice: int) -> list[int]:
    """掷 `n_dice` 个六面骰子并返回结果。"""
    return [random.randint(1, 6) for _ in range(n_dice)]

if __name__ == "__main__":
    print(f"\n---\n\n🔑 骰子投掷器访问令牌：\n\n{access_token}\n\n---\n")
    mcp.run(transport="http", port=8000)

See all 23 lines

客户端身份验证
如果您尝试使用我们之前编写的相同 OpenAI 代码调用经过身份验证的服务器，您将收到类似以下的错误：
pythonAPIStatusError: Error code: 424 - {
    "error": {
        "message": "从 MCP 服务器获取工具列表时出错： 'dice_server'。Http 状态代码：401（未授权）",
        "type": "external_connector_error",
        "param": "tools",
        "code": "http_error"
    }
}

如预期的那样，服务器拒绝了请求，因为它未经身份验证。
要验证客户端，您可以在 Authorization 请求头中使用 Bearer 方案传递令牌：
from openai import OpenAI

# 您的服务器 URL（替换为您的实际 URL）
url = 'https://your-server-url.com'

# 您的访问令牌（替换为您的实际令牌）
access_token = 'your-access-token'

client = OpenAI()

resp = client.responses.create(
    model="gpt-4.1",
    tools=[
        {
            "type": "mcp",
            "server_label": "dice_server",
            "server_url": f"{url}/mcp/",
            "require_approval": "never",
            "headers": {
                "Authorization": f"Bearer {access_token}"
            }
        },
    ],
    input="投几个骰子！",
)

print(resp.output_text)

See all 27 lines
现在您应该在输出中看到骰子投掷结果。
Gemini SDK 🤝 FastMCP

Pydantic AI 🤝 FastMCP

x

---

创建服务器
部署服务器
调用服务器
身份验证
服务器身份验证
客户端身份验证
AI SDK
Anthropic API 🤝 FastMCP

将 FastMCP 服务器连接到 Anthropic API

Anthropic 的 Messages API 支持将 MCP 服务器作为远程工具源。本教程将向您展示如何创建一个 FastMCP 服务器并将其部署到公共 URL，然后如何从 Messages API 调用它。
目前，MCP 连接器仅从 MCP 服务器访问工具——它查询 list_tools 端点并将这些功能公开给 Claude。其他 MCP 功能（如资源和提示）目前不受支持。您可以在 Anthropic 文档 中了解更多关于 MCP 连接器的信息。

创建服务器
首先，创建一个包含您要公开的工具的 FastMCP 服务器。在此示例中，我们将创建一个服务器，它包含一个单一的工具，这个工具能掷骰子。
server.py
import random
from fastmcp import FastMCP

mcp = FastMCP(name="骰子投掷器")

@mcp.tool
def roll_dice(n_dice: int) -> list[int]:
    """掷 `n_dice` 个六面骰子，并返回结果。"""
    return [random.randint(1, 6) for _ in range(n_dice)]

if __name__ == "__main__":
    mcp.run(transport="http", port=8000)

部署服务器
您的服务器必须部署到公共 URL，以便 Anthropic 访问它。MCP 连接器支持 SSE 和 Streamable HTTP 传输。
对于开发，您可以使用 ngrok 等工具临时将本地运行的服务器暴露到互联网。我们将在此示例中这样做（您可能需要安装 ngrok 并创建一个免费账户），但您可以使用任何其他方法来部署您的服务器。
假设您将上述代码保存为 server.py，您可以在两个单独的终端中运行以下两个命令来部署您的服务器并将其暴露到互联网：
FastMCP server
ngrok
python server.py

这会将您的未认证服务器暴露到互联网。如果您了解风险，只能在安全环境中运行此命令。

调用服务器
要在 MCP 服务器上使用 Messages API，您需要安装 Anthropic Python SDK（FastMCP 不包含）：
pip install anthropic

您还需要向 Anthropic 进行身份验证。您可以通过设置 ANTHROPIC_API_KEY 环境变量来实现。请查阅 Anthropic SDK 文档以获取更多信息。
export ANTHROPIC_API_KEY="your-api-key"

以下是如何从 Python 调用您的服务器的示例。请注意，您需要将 https://your-server-url.com 替换为您服务器的实际 URL。此外，我们使用 /mcp/ 作为端点，因为我们部署了一个使用默认路径的 streamable-HTTP 服务器；如果您自定义了服务器的部署，可能需要使用不同的端点。目前您还必须包含带有 anthropic-beta 标头的 extra_headers 参数。
import anthropic
from rich import print

# 您的服务器网址（请将其替换为您的实际网址）
url = 'https://your-server-url.com'

client = anthropic.Anthropic()

response = client.beta.messages.create(
    model="claude-sonnet-4-20250514",
    max_tokens=1000,
    messages=[{"role": "user", "content": "Roll a few dice!"}],
    mcp_servers=[
        {
            "type": "url",
            "url": f"{url}/mcp/",
            "name": "dice-server",
        }
    ],
    extra_headers={
        "anthropic-beta": "mcp-client-2025-04-04"
    }
)

print(response.content)

如果您运行此代码，您将看到如下输出：
我来为您掷骰子！让我使用掷骰子工具。

我掷了 3 个骰子，得到：4、2、6

结果是 4、2 和 6。您想让我再掷一次或掷不同数量的骰子吗？

身份验证
版本
2.6.0
新增
MCP 连接器通过授权令牌支持 OAuth 身份验证，这意味着您可以保护您的服务器，同时仍然允许 Anthropic 访问它。

服务器身份验证
向服务器添加身份验证的最简单方法是使用承载令牌方案。
在此示例中，我们将使用 FastMCP 的 RSAKeyPair 实用程序快速生成我们自己的令牌，但这可能不适合生产使用。有关更多详细信息，请参阅完整的服务器端令牌验证文档。
我们将首先创建一个 RSA 密钥对来签名和验证令牌。
from fastmcp.server.auth.providers.jwt import RSAKeyPair

key_pair = RSAKeyPair.generate()
access_token = key_pair.create_token(audience="dice-server")

FastMCP 的 RSAKeyPair 实用程序仅用于开发和测试。
接下来，我们将创建一个 JWTVerifier 来验证服务器。
from fastmcp import FastMCP
from fastmcp.server.auth import JWTVerifier

auth = JWTVerifier(
    public_key=key_pair.public_key,
    audience="dice-server",
)

mcp = FastMCP(name="Dice Roller", auth=auth)

这是一个可以复制/粘贴的完整示例。为了简单起见，仅出于此示例的目的，它将令牌打印到控制台。不要在生产环境中这样做！
server.py
from fastmcp import FastMCP
from fastmcp.server.auth import JWTVerifier
from fastmcp.server.auth.providers.jwt import RSAKeyPair
import random

key_pair = RSAKeyPair.generate()
access_token = key_pair.create_token(audience="dice-server")

auth = JWTVerifier(
    public_key=key_pair.public_key,
    audience="dice-server",
)

mcp = FastMCP(name="Dice Roller", auth=auth)

@mcp.tool
def roll_dice(n_dice: int) -> list[int]:
    """掷 `n_dice` 个六面骰子，并返回结果。"""
    return [random.randint(1, 6) for _ in range(n_dice)]

if __name__ == "__main__":
    print(f"\n---\n\n🔑 骰子投掷器 access token:\n\n{access_token}\n\n---\n")
    mcp.run(transport="http", port=8000)

See all 23 lines

客户端身份验证
如果您尝试使用我们之前编写的相同 Anthropic 代码调用经过身份验证的服务器，您将收到一个错误，指示服务器因未进行身份验证而拒绝了请求。
Error code: 400 - {
    "type": "error", 
    "error": {
        "type": "invalid_request_error", 
        "message": "MCP 服务器“dice-server”需要进行身份验证。请提供授权令牌。",
    },
}

要验证客户端，您可以在 MCP 服务器配置中使用 authorization_token 参数传递令牌：
import anthropic
from rich import print

# 您的服务器网址（请将其替换为您的实际网址）
url = 'https://your-server-url.com'

# 您的访问令牌（请将此处的“令牌”替换为您实际的令牌）
access_token = 'your-access-token'

client = anthropic.Anthropic()

response = client.beta.messages.create(
    model="claude-sonnet-4-20250514",
    max_tokens=1000,
    messages=[{"role": "user", "content": "Roll a few dice!"}],
    mcp_servers=[
        {
            "type": "url",
            "url": f"{url}/mcp/",
            "name": "dice-server",
            "authorization_token": access_token
        }
    ],
    extra_headers={
        "anthropic-beta": "mcp-client-2025-04-04"
    }
)

print(response.content)

您现在应该在输出中看到掷骰子结果。
Goose 🤝 FastMCP

Gemini SDK 🤝 FastMCP

x

---
