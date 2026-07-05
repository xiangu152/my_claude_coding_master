---
title: "Windows 下安装 Redis"
source: "https://redis.com.cn/topics/windows-install-redis.html"
version: "7.x"
---

# Windows 下安装 Redis

> 原始文档来源：https://redis.com.cn/topics/windows-install-redis.html

---

Windows环境中安装Redis

1、下载Redis

下载地址：https://github.com/dmajkic/redis/downloads 根据实际情况下载32bit和64bit操作系统对应的Redis版本，然后将解压的所有文件拷贝到自定义盘符目录取名redis。
如 D:\reids\
重要文件介绍：

redis-benchmark.exe   # 性能测试工具
redis-check-aof.exe   # aof文件修复工具
redischeck-dump.exe   # RDB文件检查工具
redis-cli.exe         # 客户端
redis-server.exe      # 服务器
redis.conf            # 配置文件

2、运行Redis

超级管理员身份打开一个cmd窗口使用cd命令切换目录到 D:\redis
运行redis-server.exe redis.conf
启动Redis

3、测试Redis

另启一个cmd窗口，原来的不要关闭，不然就无法访问服务端了。
切换到redis目录下运行redis-cli.exe -h 127.0.0.1 -p 6379 。
设置键值对 set myKey yanfadi
取出键值对 get myKey

运行Redis
