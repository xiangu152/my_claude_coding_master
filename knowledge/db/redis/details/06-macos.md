---
title: "macOS 下安装 Redis"
source: "https://redis.com.cn/topics/install-redis-on-mac-os.html"
version: "7.x"
---

# macOS 下安装 Redis

> 原始文档来源：https://redis.com.cn/topics/install-redis-on-mac-os.html

---

在 macOS 上安装 Redis

本指南将展示如何使用 Homebrew 在 macOS 上安装 Redis。Homebrew 是 macOS 上安装 Redis 的最简便方式。如果更倾向于从源代码构建 Redis，请参阅 从源代码安装 Redis。

先决条件

首先，确保已安装 Homebrew。打开终端并运行：

brew --version

如果此命令失败，请先 按照 Homebrew 安装说明 完成安装。

安装

在终端中运行以下命令：

brew install redis

这将把 Redis 安装到你的系统中。

前台启动和停止 Redis

要测试 Redis 安装，可以直接运行 redis-server 可执行文件：

redis-server

如果成功，你将看到 Redis 的启动日志，且 Redis 会以前台模式运行。

通过按下 Ctrl-C 可以停止 Redis。

使用 launchd 启动和停止 Redis

作为前台运行的替代方案，可以使用 launchd 在后台启动 Redis：

brew services start redis

这将启动 Redis 并在登录时自动重启。可以通过以下命令检查 Redis 的状态：

brew services info redis

如果服务正在运行，输出将类似于：

redis (homebrew.mxcl.redis)
Running: ?
Loaded: ?
User: miranda
PID: 67975

停止服务时运行：

brew services stop redis

连接到 Redis

Redis 运行后，可以通过 redis-cli 测试连接：

redis-cli

这将打开 Redis 的 REPL。尝试运行以下命令：

127.0.0.1:6379> lpush demos redis-macOS-demo
OK
127.0.0.1:6379> rpop demos
"redis-macOS-demo"

下一步

安装并运行 Redis 实例后，你可能需要：

尝试 Redis CLI 教程
使用 Redis 客户端进行连接
