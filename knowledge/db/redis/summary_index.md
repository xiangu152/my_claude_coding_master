# Redis 知识库

> **版本**: 7.x | **来源**: redis.com.cn | **文件数**: 40 | **总大小**: ~524KB

## 文档层级结构

```
~/.claude/knowledge/db/redis/
├── summary_index.md              ← 本文件（分层抽象）
└── details/                      ← 完整原始文档（40 文件）
    ├── 01~02: 介绍              ← Redis 概述 + 数据类型
    ├── 03~06: 入门              ← 安装 + 配置
    ├── 07~17: 编程              ← 管道/发布订阅/事务/Lua/内存/过期/LRU/批量/分区/分布式锁/通知
    ├── 18~29: 管理              ← CLI/复制/持久化/安全/Sentinel/延迟/基准测试/版本
    ├── 30~31: 集群              ← 集群教程 + 集群规范
    ├── 32~33: 协议与内部        ← 协议规范 + 内部机制
    ├── 34~38: 进阶              ← 性能优化/最佳实践/问题排查/缓存问题/Redis 7.0
    └── 39~40: 其他              ← PHP 扩展 / 面试题
```

## 架构总览

```
Redis 数据库
├── 数据类型
│   ├── String          → 最基本类型，最大 512MB
│   ├── Hash            → 字段-值映射表
│   ├── List            → 有序字符串列表（双端操作）
│   ├── Set             → 无序不重复集合
│   ├── Sorted Set      → 带分数的有序集合
│   ├── Bitmap          → 位操作
│   ├── HyperLogLog     → 基数估算
│   ├── Stream          → 日志型数据结构（5.0+）
│   └── Geospatial      → 地理位置
│
├── 编程模型
│   ├── Pipelining      → 批量发送命令，减少 RTT
│   ├── Pub/Sub         → 发布/订阅消息模式
│   ├── Transactions    → MULTI/EXEC 事务（非回滚）
│   ├── Lua Scripting   → 服务器端脚本（原子执行）
│   └── Keyspace Notifications → 键空间事件通知
│
├── 持久化与复制
│   ├── RDB             → 快照（定时全量）
│   ├── AOF             → 追加日志（每条/每秒同步）
│   ├── Replication     → 主从复制（异步）
│   └── Sentinel        → 高可用（自动故障转移）
│
├── 集群
│   ├── Cluster         → 分布式（16384 哈希槽）
│   ├── MOVED 重定向    → 键不在当前节点
│   ├── ASK 重定向      → 迁移中的键
│   └── Resharding      → 在线重新分片
│
├── 性能与优化
│   ├── 内存优化        → 编码优化/压缩/过期策略
│   ├── LRU 淘汰        → 内存满时的淘汰策略
│   ├── Pipelining      → 减少网络往返
│   └── Benchmarks      → redis-benchmark 性能测试
│
├── 安全
│   ├── ACL             → 访问控制列表（6.0+）
│   ├── TLS/SSL         → 传输加密
│   ├── AUTH            → 密码认证
│   └── rename-command  → 禁用/重命名危险命令
│
├── 管理工具
│   ├── redis-cli       → 命令行客户端
│   ├── redis-benchmark → 性能测试工具
│   ├── redis-check-*   → 数据修复工具
│   └── CONFIG          → 运行时配置管理
│
└── 客户端连接
    ├── 连接数限制      → maxclients
    ├── 超时设置        → timeout / tcp-keepalive
    ├── 连接池          → 客户端连接池管理
    └── 协议            → RESP (Redis Serialization Protocol)
```

## 文件索引

| # | 文件名 | 主题 | 大小 |
|---|--------|------|------|
| **介绍** | | | |
| 01 | 01-why-redis.md | 为什么要选择 Redis | 6KB |
| 02 | 02-data-types.md | Redis 数据类型介绍 | 3KB |
| **入门** | | | |
| 03 | 03-install.md | Redis 安装教程 | 4KB |
| 04 | 04-config.md | Redis 配置详解 | 2KB |
| 05 | 05-windows.md | Windows 下安装 Redis | 1KB |
| 06 | 06-macos.md | macOS 下安装 Redis | 1KB |
| **编程** | | | |
| 07 | 07-pipelining.md | 管道 (Pipelining) | 2KB |
| 08 | 08-pubsub.md | 发布/订阅 (Pub/Sub) | 2KB |
| 09 | 09-transactions.md | Redis 事务 | 3KB |
| 10 | 10-lua-scripting.md | Redis Lua 脚本 | 2KB |
| 11 | 11-memory-optimization.md | 内存优化 | 5KB |
| 12 | 12-expire.md | 过期 (Expires) | 1KB |
| 13 | 13-lru-cache.md | Redis 作为 LRU 缓存 | 3KB |
| 14 | 14-mass-insert.md | Redis 如何插入大量数据 | 2KB |
| 15 | 15-partitioning.md | Redis 分区 (Partitioning) | 3KB |
| 16 | 16-distributed-locks.md | Redis 分布式锁 | 7KB |
| 17 | 17-notifications.md | Redis 键空间通知 | 4KB |
| **管理** | | | |
| 18 | 18-rediscli.md | Redis-cli | 14KB |
| 19 | 19-replication.md | 复制 (Replication) | 8KB |
| 20 | 20-persistence.md | 持久化 (Persistence) | 6KB |
| 21 | 21-admin.md | Redis 管理 | 2KB |
| 22 | 22-security.md | 安全 (Security) | 6KB |
| 23 | 23-encryption.md | 加密 (Encryption) | 1KB |
| 24 | 24-signals.md | 信号处理 | 2KB |
| 25 | 25-connections.md | 连接处理 | 4KB |
| 26 | 26-sentinel.md | 高可用 Sentinel | 25KB |
| 27 | 27-latency.md | 延迟监控 | 4KB |
| 28 | 28-benchmarks.md | 性能测试 | 12KB |
| 29 | 29-releases.md | 版本号 | 1KB |
| **集群** | | | |
| 30 | 30-cluster-tutorial.md | Redis 集群教程 | 38KB |
| 31 | 31-cluster-spec.md | Redis 集群规范 | 28KB |
| **协议与内部** | | | |
| 32 | 32-protocol.md | Redis 协议规范 (RESP) | 11KB |
| 33 | 33-internals.md | Redis 内部机制 | 1KB |
| **进阶** | | | |
| 34 | 34-performance.md | Redis 性能优化指南 | 2KB |
| 35 | 35-best-practices.md | Redis 最佳实践 | 2KB |
| 36 | 36-troubleshooting.md | Redis 常见问题排查 | 3KB |
| 37 | 37-cache-problems.md | Redis 缓存问题排查 | 8KB |
| 38 | 38-redis7-features.md | Redis 7.0 新特性 | 3KB |
| **其他** | | | |
| 39 | 39-php-extension.md | PHP Redis 扩展 | 1KB |
| 40 | 40-interview-questions.md | Redis 面试题 | 9KB |

## 关键概念速查

| 概念 | 定义 | 详情文件 |
|------|------|----------|
| **String** | 最基本类型，可存字符串/整数/浮点数，最大 512MB | 02 |
| **Hash** | 字段-值映射表，适合存储对象 | 02 |
| **List** | 有序字符串列表，支持双端操作 LPUSH/RPUSH | 02 |
| **Set** | 无序不重复集合，支持交/并/差集运算 | 02 |
| **Sorted Set** | 带分数的有序集合，按分数排序 | 02 |
| **Pipelining** | 批量发送命令，减少网络往返延迟 | 07 |
| **Pub/Sub** | 发布/订阅模式，消息不持久化 | 08 |
| **Transaction** | MULTI/EXEC 事务，不支持回滚，支持 WATCH 乐观锁 | 09 |
| **Lua Scripting** | 服务器端脚本，原子执行复杂逻辑 | 10 |
| **RDB** | 定时全量快照，恢复快但可能丢数据 | 20 |
| **AOF** | 追加写日志，更安全但文件更大 | 20 |
| **Replication** | 主从复制，异步同步，读写分离 | 19 |
| **Sentinel** | 高可用方案，自动故障转移和通知 | 26 |
| **Cluster** | 分布式方案，16384 哈希槽分片，支持在线重分片 | 30, 31 |
| **LRU** | 内存淘汰策略：noeviction/volatile-lru/allkeys-lru/random | 13 |
| **ACL** | 访问控制列表（6.0+），细粒度权限控制 | 22 |
| **RESP** | Redis 序列化协议，客户端-服务端通信格式 | 32 |

## 速查表

### 常用命令
```bash
redis-cli                         # 连接 Redis
redis-cli -h host -p port -a pwd  # 指定连接参数
redis-cli --scan --pattern '*'    # 扫描所有键
redis-cli INFO                    # 查看服务器信息
redis-cli DBSIZE                  # 查看键数量
redis-cli MONITOR                 # 实时监控命令
redis-benchmark -n 100000         # 性能测试
```

### 数据操作
```redis
SET key value                     # 设置字符串
GET key                           # 获取字符串
HSET hash field value             # 设置哈希字段
HGET hash field                   # 获取哈希字段
LPUSH list value                  # 列表左端插入
RPUSH list value                  # 列表右端插入
SADD set member                   # 集合添加成员
ZADD zset score member            # 有序集合添加
EXPIRE key seconds                # 设置过期时间
TTL key                           # 查看剩余时间
```

### 配置 (redis.conf)
```
bind 127.0.0.1                    # 绑定地址
port 6379                         # 端口
maxmemory 256mb                   # 最大内存
maxmemory-policy allkeys-lru      # 淘汰策略
appendonly yes                    # 开启 AOF
requirepass mypassword            # 设置密码
```

### 复制与高可用
```
# 从服务器配置
replicaof master-ip 6379

# Sentinel 配置
sentinel monitor mymaster 127.0.0.1 6379 2
sentinel down-after-milliseconds mymaster 5000
sentinel failover-timeout mymaster 60000
```
