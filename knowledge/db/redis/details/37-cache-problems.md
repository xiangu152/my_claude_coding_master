---
title: "Redis 缓存问题排查"
source: "https://redis.com.cn/topics/redis-cache-problems.html"
version: "7.x"
---

# Redis 缓存问题排查

> 原始文档来源：https://redis.com.cn/topics/redis-cache-problems.html

---

Redis 缓存穿透/击穿/雪崩：原理与 6 种解决方案全解析

摘要：缓存穿透、缓存击穿、缓存雪崩是 Redis 应用中最经典的三类高并发问题。本文从底层原理出发，结合 Redis 7.x 特性，提供 6 种经过生产验证的解决方案，并附带可执行的命令示例与架构图解。

一、三大问题速查对比
问题	触发条件	影响范围	核心危害
缓存穿透	查询不存在的数据	数据库承受非法请求	DB 被打穿，服务雪崩
缓存击穿	热点 Key突然过期	单个热点数据	瞬间流量直击 DB
缓存雪崩	大量 Key 同时过期	整片缓存数据	全量请求压垮 DB
问题决策流程
用户请求
    │
    ▼
缓存是否命中？
    ├─ 是 → 返回缓存数据 ✅
    └─ 否 → 数据是否存在？
            ├─ 不存在 → 缓存穿透 ⚠️
            └─ 存在但过期 → 是热点 Key？
                    ├─ 是单点 → 缓存击穿 🔥
                    └─ 大面积 → 缓存雪崩 ❄️

二、缓存穿透：请求"幽灵数据"
2.1 原理剖析

缓存穿透指查询一个数据库中不存在的数据。由于缓存中也没有，请求会直接打到数据库。攻击者可能利用这一点，构造大量不存在的 Key 进行恶意请求，导致数据库压力剧增。

典型时序：

用户          Redis缓存        MySQL数据库
 │               │                │
 │── GET id=-99999 ──>│                │
 │<──── nil ──────────│                │
 │               │                │
 │─────────────── SELECT * FROM user WHERE id=-99999 ──>│
 │<────────────── Empty Set ────────────────────────────│
 │               │                │
 │               │   每次请求都查DB！  │

2.2 解决方案
*方案 1：布隆过滤器（Bloom Filter）

在缓存层之前加一道布隆过滤器，快速判断 Key 是否可能存在。

# Redis 4.0+ 可通过 RedisBloom 模块实现
# 安装模块后
127.0.0.1:6379> BF.RESERVE user_filter 0.001 1000000
OK

# 添加元素
127.0.0.1:6379> BF.ADD user_filter user:1001
(integer) 1

# 检查可能存在
127.0.0.1:6379> BF.EXISTS user_filter user:1001
(integer) 1

# 检查一定不存在
127.0.0.1:6379> BF.EXISTS user_filter user:-99999
(integer) 0

原理：布隆过滤器使用位数组和多个哈希函数，可能存在误判（false positive），但不会漏判（false negative）。即：返回"不存在"的数据一定不存在；返回"可能存在"的才需要查缓存/DB。

注意：BF.* 命令需要安装 RedisBloom 模块。安装后在 redis.conf 中添加 loadmodule /path/to/redisbloom.so。

*方案 2：缓存空值（Cache Null）

对于数据库中确实不存在的数据，也将"空值"写入缓存，设置较短的过期时间（如 60 秒）。

# 查询数据库为空后，缓存空值
127.0.0.1:6379> SET user:-99999 "__NULL__" EX 60 NX
OK

# 后续相同请求直接命中空值缓存
127.0.0.1:6379> GET user:-99999
"__NULL__"

注意：空值缓存时间不宜过长，防止真实数据新增后长期无法访问。

三、缓存击穿：热点 Key"猝死"
3.1 原理剖析

某个极高访问量的热点 Key 在缓存中突然过期（或尚未加载），此时大量并发请求同时到达，全部穿透到数据库。

并发时序：

时间点: 热点 Key 过期瞬间

用户1 ──> Redis: GET hot_product:1001
          返回: nil
用户2 ──> Redis: GET hot_product:1001
          返回: nil
用户3 ──> Redis: GET hot_product:1001
          返回: nil

用户1 ──> MySQL: SELECT * FROM product WHERE id=1001
用户2 ──> MySQL: SELECT * FROM product WHERE id=1001
用户3 ──> MySQL: SELECT * FROM product WHERE id=1001

          MySQL: 瞬间N个线程查同一行！🔥

3.2 解决方案
*方案 3：互斥锁（Mutex / 分布式锁）

只让一个线程去查数据库并重建缓存，其他线程等待缓存重建完成。

# 尝试获取互斥锁
127.0.0.1:6379> SET lock:hot_product:1001 1 EX 10 NX
OK

# 获取锁成功的线程查询DB并重建缓存
127.0.0.1:6379> SET hot_product:1001 "<product_data>" EX 3600
OK

# 删除锁
127.0.0.1:6379> DEL lock:hot_product:1001
(integer) 1

# Python 伪代码
import redis
import time

r = redis.Redis()

def get_hot_product(product_id):
    cache_key = f"hot_product:{product_id}"
    lock_key = f"lock:{cache_key}"

    data = r.get(cache_key)
    if data:
        return data

    # 尝试获取锁（SET NX EX）
    locked = r.set(lock_key, 1, nx=True, ex=10)
    if locked:
        try:
            # 只有获得锁的线程查DB
            data = query_database(product_id)
            r.set(cache_key, data, ex=3600)
            return data
        finally:
            r.delete(lock_key)
    else:
        # 未获得锁，等待后重试
        time.sleep(0.1)
        return get_hot_product(product_id)  # 递归重试

*方案 4：逻辑过期（Logical Expiration）

不设置 Redis 的 TTL，而是在 Value 中存储逻辑过期时间，由业务层判断是否"过期"并异步重建。

# Value 结构：{"data": "...", "expire_time": 1716540000}
127.0.0.1:6379> SET hot_product:1001 '{"data": "<product_data>", "expire_time": 1716543600}'
OK

# 业务层判断逻辑
import json, time

def get_product_logical(product_id):
    cache_key = f"hot_product:{product_id}"
    raw = r.get(cache_key)

    if not raw:
        return query_database(product_id)

    value = json.loads(raw)
    now = int(time.time())

    if value["expire_time"] > now:
        return value["data"]  # 未过期，直接返回
    else:
        # 已过期但返回旧数据，异步启动线程重建
        threading.Thread(target=rebuild_cache, args=(product_id,)).start()
        return value["data"]  # 返回"脏"数据，保证可用性

四、缓存雪崩：集体"暴雷"
4.1 原理剖析

缓存雪崩指大量 Key 在同一时间集中过期，或者 Redis 集群整体宕机，导致所有请求同时涌向数据库。

状态对比：

正常状态                            雪崩瞬间
────────────────────────────────    ────────────────────────────────

请求1 ──> Redis缓存                 请求1 ──> Redis? (过期/宕机)
请求2 ──> Redis缓存    ────────>    请求2 ──> Redis? (过期/宕机)
请求3 ──> Redis缓存                 请求3 ──> Redis? (过期/宕机)
    │                                   │
    ▼                                   ▼
MySQL: 低负载                       MySQL: 10000+ QPS
                                       │
                                       ▼
                                  连接池耗尽
                                       │
                                       ▼
                                  服务超时/宕机 ❄️

4.2 解决方案
*方案 5：过期时间加随机偏移

避免大量 Key 同时失效，在基础过期时间上增加随机偏移量。

# 基础过期时间 3600 秒，随机增加 0-600 秒
# 实际过期时间范围：3600 ~ 4200 秒
127.0.0.1:6379> SET product:1001 "data" EX 3600
OK

# Python 中实现
import random

base_ttl = 3600
random_offset = random.randint(0, 600)  # 0~10分钟随机
final_ttl = base_ttl + random_offset
r.setex(f"product:{id}", final_ttl, data)

*方案 6：多级缓存架构

构建 L1（本地缓存，如 Caffeine/Guava）+ L2（Redis 分布式缓存）+ L3（数据库）的三级缓存体系。

架构层级：

用户请求
    │
    ▼
┌─────────────────┐
│ 本地缓存 Caffeine │ ← L1，微秒级，进程内
│   (Guava/Ehcache) │
└────────┬────────┘
         │ 未命中
         ▼
┌─────────────────┐
│   Redis 缓存     │ ← L2，毫秒级，分布式
│  (共享缓存层)     │
└────────┬────────┘
         │ 未命中
         ▼
┌─────────────────┐
│     数据库        │ ← L3，毫秒级，持久化
│   (MySQL/PostgreSQL)
└─────────────────┘

// Spring Boot + Caffeine + Redis 多级缓存示例
@Cacheable(value = "product", key = "#id", cacheManager = "caffeineCacheManager")
public Product getProduct(Long id) {
    // 先查 Caffeine（本地，微秒级）
    // 未命中则查 Redis
    String cacheData = redisTemplate.opsForValue().get("product:" + id);
    if (cacheData != null) {
        return JSON.parseObject(cacheData, Product.class);
    }
    // 未命中则查数据库
    Product product = productDao.findById(id);
    redisTemplate.opsForValue().set("product:" + id, JSON.toJSONString(product), 1, TimeUnit.HOURS);
    return product;
}

五、6 种方案对比与选型指南
方案	适用问题	复杂度	性能影响	数据一致性	推荐场景
布隆过滤器	缓存穿透	中	低（O(1)）	可能误判	海量非法请求防御
缓存空值	缓存穿透	低	低	弱（有延迟）	简单场景、少量空值
互斥锁	缓存击穿	中	中（有等待）	强	热点 Key 重建
逻辑过期	缓存击穿	高	低	弱（脏读）	极高可用性要求
随机偏移	缓存雪崩	低	无	强	批量 Key 过期
多级缓存	缓存雪崩	高	极低	弱（最终一致）	大型高并发系统
六、Redis 命令速查表
命令	用途	示例
SET key value EX seconds	设置带过期时间的键	SET user:1 "Alice" EX 3600
GET key	获取键值	GET user:1
SET key value NX EX seconds	分布式锁（不存在才设置）	SET lock:product:1001 1 EX 10 NX
DEL key	删除键	DEL lock:product:1001
EXPIRE key seconds	设置过期时间	EXPIRE user:1 3600
TTL key	查看剩余过期时间	TTL user:1
BF.RESERVE	创建布隆过滤器（需 RedisBloom）	BF.RESERVE user_filter 0.001 1000000
BF.ADD	向布隆过滤器添加元素	BF.ADD user_filter user:1001
BF.EXISTS	检查元素可能存在	BF.EXISTS user_filter user:1001
INFO memory	查看内存使用	INFO memory
INFO stats	查看统计信息	INFO stats
MONITOR	实时监控命令	MONITOR
SLOWLOG GET	查看慢查询日志	SLOWLOG GET 10
七、生产环境 checklist
# 1. 检查 Redis 内存使用，防止 OOM
127.0.0.1:6379> INFO memory

# 2. 查看 Key 过期策略
127.0.0.1:6379> CONFIG GET maxmemory-policy

# 3. 监控慢查询，发现潜在热点 Key
127.0.0.1:6379> SLOWLOG GET 20

# 4. 检查连接数是否接近上限
127.0.0.1:6379> INFO clients

# 5. 查看持久化状态
127.0.0.1:6379> INFO persistence

八、总结

缓存穿透、击穿、雪崩本质上是高并发场景下缓存层与数据库层的协作问题。

问题	核心解决思路
缓存穿透	拦截非法请求 → 布隆过滤器 / 缓存空值
缓存击穿	保护单个热点 → 互斥锁 / 逻辑过期
缓存雪崩	分散过期时间 → 随机偏移 / 多级缓存

在实际生产环境中，通常需要组合使用多种方案，例如：

布隆过滤器 + 缓存空值 → 防御穿透
互斥锁 + 逻辑过期 → 保护热点
随机偏移 + 多级缓存 + Redis Cluster → 防止雪崩
相关阅读
Redis 持久化机制详解 — RDB、AOF、混合持久化原理
Redis 分布式锁实现 — Redlock 算法与注意事项
Redis 内存优化策略 — LRU/LFU、内存碎片整理
Redis 集群规范 — 哈希槽、ASK/MOVED 重定向
Redis 面试题精选 — 高频考点汇总
Redis 最佳实践
Redis 故障排查 

作者注：本文所有命令均基于 Redis 7.x 测试通过。BF.* 系列命令需要安装 RedisBloom 模块。不同版本命令可能存在差异，请以实际环境为准。
