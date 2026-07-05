---
title: "部署"
source: "https://langchain-doc.cn/v1/python/langchain/deploy.html"
version: "v1"
---

# 部署

> 原始文档来源：https://langchain-doc.cn/v1/python/langchain/deploy.html

---
部署

好的，这是您内容的标准 Markdown 格式和中文翻译：

部署

LangSmith 是将 Agent 转化为生产系统的最快方式。传统的托管平台是为无状态、短生命周期的 Web 应用而构建的，而 LangGraph 则是专为有状态、长生命周期的 Agent 而设计，因此您可以在几分钟内完成从代码仓库到可靠的云部署。

先决条件

在开始之前，请确保您具备以下条件：

一个 GitHub 账户
一个 LangSmith 账户（免费注册）
部署您的 Agent
1. 在 GitHub 上创建一个代码仓库

您的应用程序代码必须位于 GitHub 仓库中才能部署到 LangSmith 上。LangSmith 支持公共和私有仓库。对于本快速入门，请首先按照 本地服务器设置指南 确保您的应用程序与 LangGraph 兼容。然后，将您的代码推送到该仓库。
