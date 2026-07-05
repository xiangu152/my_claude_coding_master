---
title: "运行本地服务器"
source: "https://langchain-doc.cn/v1/python/langgraph/local-server.html"
version: "v1"
---

# 运行本地服务器
> 原始文档来源：https://langchain-doc.cn/v1/python/langgraph/local-server.html

# 需要Python >= 3.11
# 需要Python >= 3.11
npx @langchain/langgraph-cli
2. 创建LangGraph应用 🌱

从new-langgraph-project-python模板创建一个新应用。这个模板演示了你可以用自己的逻辑扩展的单节点应用程序。 脚本语言

langgraph new path/to/your/app --template new-langgraph-project-python

附加模板
如果你使用langgraph new而不指定模板，你将看到一个交互式菜单，允许你从可用模板列表中进行选择。


从new-langgraph-project-js模板创建一个新应用。这个模板演示了你可以用自己的逻辑扩展的单节点应用程序。

npm create langgraph
3. 安装依赖

在你的新LangGraph应用的根目录中，以edit模式安装依赖，以便服务器使用你的本地更改：

cd path/to/your/app
pip install -e .
cd path/to/your/app
uv add .
cd path/to/your/app
npm install
4. 创建.env文件

你将在新LangGraph应用的根目录中找到一个.env.example文件。在新LangGraph应用的根目录中创建一个.env文件，并将.env.example文件的内容复制到其中，填写必要的API密钥： 软件

LANGSMITH_API_KEY=lsv2...
5. 启动LangGraph服务器 🚀

在本地启动LangGraph API服务器：

langgraph dev
npx @langchain/langgraph-cli dev

示例输出：

>    Ready!
>
>    - API: [http://localhost:2024](http://localhost:2024/)
>
>    - Docs: http://localhost:2024/docs
>
>    - LangGraph Studio Web UI: https://smith.langchain.com/studio/?baseUrl=http://127.0.0.1:2024

langgraph dev命令以内存模式启动LangGraph服务器。此模式适合开发和测试目的。对于生产使用，请部署具有持久存储后端访问权限的LangGraph服务器。有关更多信息，请参阅托管概述。

6. 在Studio中测试你的应用程序

Studio是一个专门的UI，你可以连接到LangGraph API服务器以可视化、交互和调试你的本地应用程序。通过访问langgraph dev命令输出中提供的URL在Studio中测试你的图：

>    - LangGraph Studio Web UI: https://smith.langchain.com/studio/?baseUrl=http://127.0.0.1:2024

对于在自定义主机/端口上运行的LangGraph服务器，请更新baseURL参数。

Safari兼容性
使用命令的--tunnel标志创建安全隧道，因为Safari在连接到localhost服务器时有限制：

langgraph dev --tunnel
7. 测试API

Python SDK（异步）

安装LangGraph Python SDK：
pip install langgraph-sdk
向助手发送消息（无线程运行）：
```
from langgraph_sdk import get_client
import asyncio
```

client = get_client(url="http://localhost:2024")

```
async def main():
    async for chunk in client.runs.stream(
        None,  # 无线程运行
        "agent", # 助手名称。在langgraph.json中定义。
        input={
        "messages": [{
            "role": "human",
            "content": "什么是LangGraph？",
            }],
        },
    ):
        print(f"接收类型为: {chunk.event} 的新事件...")
        print(chunk.data)
        print("\n\n")
```

asyncio.run(main())

Python SDK（同步）

安装LangGraph Python SDK：
pip install langgraph-sdk
向助手发送消息（无线程运行）：
from langgraph_sdk import get_sync_client
client = get_sync_client(url="http://localhost:2024")

```
for chunk in client.runs.stream(
    None,  # 无线程运行
    "agent", # 助手名称。在langgraph.json中定义。
    input={
        "messages": [{
            "role": "human",
            "content": "什么是LangGraph？",
        }],
    },
    stream_mode="messages-tuple",
```
):
```
    print(f"接收类型为: {chunk.event} 的新事件...")
    print(chunk.data)
    print("\n\n")
```

REST API

curl -s --request POST \
```
    --url "http://localhost:2024/runs/stream" \
    --header 'Content-Type: application/json' \
    --data "{
        \"assistant_id\": \"agent\",
        \"input\": {
            \"messages\": [
                {
                    \"role\": \"human\",
                    \"content\": \"什么是LangGraph？\" 
                }
            ]
        },
        \"stream_mode\": \"messages-tuple\" 
    }"
```

JavaScript SDK

安装LangGraph JS SDK：
npm install @langchain/langgraph-sdk
向助手发送消息（无线程运行）：
const { Client } = await import("@langchain/langgraph-sdk");

// 只有在调用langgraph dev时更改了默认端口时才设置apiUrl
const client = new Client({ apiUrl: "http://localhost:2024"});

const streamResponse = client.runs.stream(
```
    null, // 无线程运行
    "agent", // 助手ID
    {
        input: {
            "messages": [
                { "role": "user", "content": "什么是LangGraph？"}
            ]
        },
        streamMode: "messages-tuple",
    }
```
);

```
for await (const chunk of streamResponse) {
    console.log(`接收类型为: ${chunk.event} 的新事件...`);
    console.log(JSON.stringify(chunk.data));
    console.log("\n\n");
```
}

REST API

curl -s --request POST \
```
    --url "http://localhost:2024/runs/stream" \
    --header 'Content-Type: application/json' \
    --data "{
        \"assistant_id\": \"agent\",
        \"input\": {
            \"messages\": [
                {
                    \"role\": \"human\",
                    \"content\": \"什么是LangGraph？\" 
                }
            ]
        },
        \"stream_mode\": \"messages-tuple\" 
    }"
```
下一步

现在你已经在本地运行了LangGraph应用程序，通过探索部署和高级功能来进一步推进你的旅程：

部署快速入门：使用LangSmith部署你的LangGraph应用。
LangSmith：了解LangSmith的基础概念。
Python SDK参考：探索Python SDK API参考。
JS/TS SDK参考：探索JS/TS SDK API参考。
