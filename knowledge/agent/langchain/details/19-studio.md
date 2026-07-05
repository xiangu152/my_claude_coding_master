---
title: "Studio"
source: "https://langchain-doc.cn/v1/python/langchain/studio.html"
version: "v1"
---

# Studio

> 原始文档来源：https://langchain-doc.cn/v1/python/langchain/studio.html

---
Studio

本指南将引导您了解如何使用 Studio 在本地可视化、交互和调试您的智能体（agent）。 编程

Studio 是我们免费使用的强大智能体 IDE，它集成了 LangSmith，可实现跟踪、评估和提示工程。您可以准确地看到您的智能体是如何思考的，跟踪每一个决策，并交付更智能、更可靠的智能体。

观看演示
先决条件

在开始之前，请确保您具备以下条件：

LangSmith 的 API 密钥（免费注册）。
设置本地 LangGraph 服务器
1. 安装 LangGraph CLI
# 需要 Python >= 3.11。
pip install --upgrade "langgraph-cli[inmem]"
2. 准备您的智能体

我们将使用以下简单的智能体作为示例： 深度Agents

agent.py
from langchain.agents import create_agent

```
def send_email(to: str, subject: str, body: str):
    """发送一封电子邮件"""
    email = {
        "to": to,
        "subject": subject,
        "body": body
    }
    # ... 邮件发送逻辑
```

    return f"Email sent to {to}"

agent = create_agent(
    "gpt-4o",
    tools=[send_email],
    system_prompt="You are an email assistant. Always use the send_email tool.",
)
3. 环境变量

在项目的根目录下创建一个 .env 文件，并填写必要的 API 密钥。我们需要将 LANGSMITH_API_KEY 环境变量设置为您从 LangSmith 获取的 API 密钥。

⚠️ 警告：

请务必不要将您的 .env 文件提交到像 Git 这样的版本控制系统！

LANGSMITH_API_KEY=lsv2...
4. 创建 LangGraph 配置文件

在您的应用目录内，创建一个名为 langgraph.json 的配置文件： 软件

langgraph.json
{
  "dependencies": ["."],
  "graphs": {
    "agent": "./src/agent.py:agent"
  },
  "env": ".env"
}

create_agent 会自动返回一个已编译的 LangGraph 图，我们可以将其传递给配置文件的 graphs 键。

ℹ️ 信息：

有关配置文件的 JSON 对象中每个键的详细解释，请参阅 LangGraph 配置文件参考。

到目前为止，我们的项目结构如下所示：

my-app/
├── src
│    └── agent.py
├── .env
└── langgraph.json
5. 安装依赖项

在您的新 LangGraph 应用的根目录下，安装依赖项：

pip	uv
shell pip pip install -e .	```shell uv
uv sync	

### 6. 在 Studio 中查看您的智能体

启动您的 LangGraph 服务器：

```shell
langgraph dev

⚠️ 警告：

Safari 会阻止到 Studio 的 localhost 连接。为了解决这个问题，请运行上述命令时带上 --tunnel 标志，以便通过安全隧道访问 Studio。

您的智能体将可通过 API (http://127.0.0.1:2024) 和 Studio UI (https://smith.langchain.com/studio/?baseUrl=http://127.0.0.1:2024) 访问：

在 Studio 中调试和交互

Studio 使您的智能体的每一步都易于观察。您可以重放任何输入并检查确切的提示、工具参数、返回值以及 token/延迟指标。如果工具抛出异常，Studio 会记录它和周围的状态，让您可以花更少的时间进行调试。

保持您的开发服务器运行，编辑提示或工具签名，并观察 Studio 热重载。从任何步骤重新运行对话线程以验证行为更改。有关更多详细信息，请参阅 管理线程。

随着您的智能体的增长，同样的视图可以从单工具演示扩展到多节点图，保持决策的清晰和可重现。

💡 提示：

要深入了解 Studio，请查看 概述页面。

💡 提示：

有关本地和已部署智能体的更多信息，请参阅 设置本地 LangGraph 服务器 和 部署。
