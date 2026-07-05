---
title: "加密 (Encryption)"
source: "https://redis.com.cn/topics/encryption.html"
version: "7.x"
---

# 加密 (Encryption)

> 原始文档来源：https://redis.com.cn/topics/encryption.html

---

Redis加密

虽然给Redis增加SSL特性被提议了很多次，然而目前我们认为只有小部分用户需要SSL支持，事实上由于不同方案侧重点不同，使用不同渠道策略可能会更好。我们在将来可能改变这个计划，但是目前推荐使用如下项目，它可以满足多数加密场景：

Spiped是一个用于创建套接字地址之间的对称加密和认证管道的工具，以便连接到一个地址的源端（例如，在本地主机上一个UNIX套接字），可以透明地建立连接到另一个地址（例如，一个不同系统上的UNIX套接字）。

该软件是与Redis本身类似架构实现的，它是一个自包含4000行C代码把单一功能做的非常好的工具。
