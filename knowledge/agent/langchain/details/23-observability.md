---
title: "可观测性"
source: "https://langchain-doc.cn/v1/python/langchain/observability.html"
version: "v1"
---

# 可观测性

> 原始文档来源：https://langchain-doc.cn/v1/python/langchain/observability.html

---
可观测性

可观测性对于理解您的 Agent 在生产环境中的行为至关重要。通过 LangChain 的 create_agent，您可以获得通过 LangSmith 提供的内置可观测性——这是一个用于追踪、调试、评估和监控您的 LLM 应用程序的强大平台。

追踪（Traces）会捕获您的 Agent 所采取的每一步，从最初的用户输入到最终响应，包括所有工具调用、模型交互和决策点。这使您能够调试 Agent、评估性能和监控使用情况。

先决条件

在开始之前，请确保您具备以下条件：

一个 LangSmith 账户（免费注册）
启用追踪 (Enable tracing)

所有 LangChain Agent 都自动支持 LangSmith 追踪。要启用它，请设置以下环境变量：

export LANGSMITH_TRACING=true
export LANGSMITH_API_KEY=<your-api-key>

ℹ️ 您可以从您的 LangSmith 设置 中获取您的 API 密钥。

快速开始

将追踪日志记录到 LangSmith 无需额外的代码。只需像往常一样运行您的 Agent 代码即可：

from langchain.agents import create_agent


```
def send_email(to: str, subject: str, body: str):
    """Send an email to a recipient."""
    # ... 电子邮件发送逻辑
    return f"Email sent to {to}"
```

```
def search_web(query: str):
    """Search the web for information."""
    # ... 网络搜索逻辑
    return f"Search results for: {query}"
```

agent = create_agent(
    model="gpt-4o",
    tools=[send_email, search_web],
    system_prompt="You are a helpful assistant that can send emails and search the web."
)

# 运行 Agent - 所有步骤都将自动被追踪
response = agent.invoke({
    "messages": [{"role": "user", "content": "Search for the latest AI news and email a summary to john@example.com"}]
})

默认情况下，追踪将记录到项目名称为 default 的项目下。要配置自定义项目名称，请参阅 记录到项目。

选择性追踪 (Trace selectively)

您可以选择使用 LangSmith 的 tracing_context 上下文管理器来仅追踪应用程序的特定调用或部分：

import langsmith as ls

# 这将**被追踪**
with ls.tracing_context(enabled=True):
    agent.invoke({"messages": [{"role": "user", "content": "Send a test email to alice@example.com"}]})

# 这将**不被追踪**（如果 LANGSMITH_TRACING 未设置）
agent.invoke({"messages": [{"role": "user", "content": "Send another email"}]})
记录到项目 (Log to a project)
静态设置 (Statically)

您可以通过设置 LANGSMITH_PROJECT 环境变量来为您的整个应用程序设置一个自定义项目名称：

export LANGSMITH_PROJECT=my-agent-project
动态设置 (Dynamically)

您可以为特定操作以编程方式设置项目名称：

import langsmith as ls

with ls.tracing_context(project_name="email-agent-test", enabled=True):
    response = agent.invoke({
        "messages": [{"role": "user", "content": "Send a welcome email"}]
    })
向追踪添加元数据 (Add metadata to traces)

您可以使用自定义元数据和标签来注释您的追踪：

response = agent.invoke(
    {"messages": [{"role": "user", "content": "Send a welcome email"}]},
    config={
        "tags": ["production", "email-assistant", "v1.0"],
        "metadata": {
            "user_id": "user_123",
            "session_id": "session_456",
            "environment": "production"
        }
    }
)

tracing_context 也接受标签和元数据以进行细粒度控制：

with ls.tracing_context(
    project_name="email-agent-test",
    enabled=True,
    tags=["production", "email-assistant", "v1.0"],
    metadata={"user_id": "user_123", "session_id": "session_456", "environment": "production"}):
    response = agent.invoke(
        {"messages": [{"role": "user", "content": "Send a welcome email"}]}
    )

这些自定义元数据和标签将附加到 LangSmith 中的追踪上。

💡 要了解更多关于如何使用追踪来调试、评估和监控您的 Agent，请参阅 LangSmith 文档。
