---
title: "Redis Lua 脚本"
source: "https://redis.com.cn/commands/eval.html"
version: "7.x"
---

# Redis Lua 脚本

> 原始文档来源：https://redis.com.cn/commands/eval.html

---

Redis EVAL 命令

执行 Lua 脚本。Redis 保证 Lua 脚本以原子方式执行。

语法
EVAL script numkeys key [key ...] arg [arg ...]

参数说明
参数	类型	必填	说明
script	String	是	Lua 脚本内容
numkeys	Integer	是	脚本中 key 参数的数量
key	String	否	key 参数，在脚本中通过 KEYS[1], KEYS[2]... 访问
arg	String	否	普通参数，在脚本中通过 ARGV[1], ARGV[2]... 访问
返回值

返回 Lua 脚本的返回值（自动转换为 Redis 协议类型）。

时间复杂度

取决于脚本内命令的复杂度总和。

⚠️ 注意：Lua 脚本执行期间 Redis 不处理其他命令。脚本应快速执行（默认最长 5 秒）。

示例
# 原子库存扣减（先判断再扣减）
> EVAL "local stock = redis.call('GET', KEYS[1]); if stock and tonumber(stock) >= tonumber(ARGV[1]) then return redis.call('DECRBY', KEYS[1], ARGV[1]) else return -1 end" 1 stock:1001 5
(integer) 45

# 原子 HGET + HSET
> EVAL "local val = redis.call('HGET', KEYS[1], ARGV[1]); redis.call('HSET', KEYS[1], ARGV[1], ARGV[2]); return val" 1 user:1001 name "NewName"
"OldName"

常见错误
脚本超时：默认 5 秒，超时后 Redis 开始回复 BUSY 错误。可用 SCRIPT KILL 终止（只杀不执行写命令的脚本）。
numkeys 与参数不匹配：导致 KEYS/ARGV 索引错误。
最佳实践
原子复杂操作：需要多个命令原子执行且带逻辑判断时，用 Lua 脚本替代 MULTI/EXEC。
安全库存扣减：GET 判断 + DECRBY 在一个脚本中原子完成，避免超卖。
脚本缓存：反复执行的脚本先用 SCRIPT LOAD 生成 SHA，再用 EVALSHA 执行，减少传输开销。
避免长脚本：脚本执行时间控制在毫秒级，避免阻塞 Redis。
FAQ

Q: EVAL 和 MULTI/EXEC 有什么区别？ A: EVAL 是 Lua 脚本，可以写逻辑判断和循环；MULTI/EXEC 只是命令排队，无逻辑控制。EVAL 原子性更强。

Q: Lua 脚本会阻塞 Redis 吗？ A: 会。脚本执行期间 Redis 单线程不处理其他命令。脚本必须快速执行。

Q: 如何在脚本中调用 Redis 命令？ A: redis.call('COMMAND', arg1, arg2) 或 redis.pcall('COMMAND', ...)（pcall 捕获错误）。

Q: SCRIPT LOAD 和 EVALSHA 怎么用？ A: SCRIPT LOAD "script" 返回 SHA，后续 EVALSHA sha numkeys key... arg... 执行，无需传输完整脚本。

相关命令
EVAL
EVALSHA
EVALSHA_RO
EVAL_RO
FCALL
FCALL_RO
FUNCTION
FUNCTION DELETE
FUNCTION DUMP
FUNCTION FLUSH
FUNCTION HELP
FUNCTION KILL
FUNCTION LIST
FUNCTION LOAD
FUNCTION RESTORE
FUNCTION STATS
SCRIPT
SCRIPT DEBUG
SCRIPT EXISTS
SCRIPT FLUSH
SCRIPT HELP
SCRIPT KILL
SCRIPT LOAD
