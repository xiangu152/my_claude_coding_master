---
title: "Redis 键空间通知"
source: "https://redis.com.cn/topics/notifications.html"
version: "7.x"
---

# Redis 键空间通知

> 原始文档来源：https://redis.com.cn/topics/notifications.html

---

Redis键空间通知

重要：键空间通知功能自2.8.0版本开始可用。

功能概述

键空间通知允许客户端订阅发布/订阅频道，以便以某种方式接收影响Redis数据集的事件。可能接收的事件示例如下：

所有影响给定键的命令。
所有接收LPUSH操作的键。
所有在数据库0中到期的键。

事件使用Redis的普通发布/订阅层传递，因此实现了发布/订阅的客户端无需修改即可使用此功能。由于Redis的发布/订阅是fire and forget，因此如果你的应用要求可靠的事件通知，目前还不能使用这个功能，也就是说，如果你的发布/订阅客户端断开连接，并在稍后重连，那么所有在客户端断开期间发送的事件将会丢失。将来有计划允许更可靠的事件传递，但可能会在更一般的层面上解决，要么为发布/订阅本身带来可靠性，要么允许Lua脚本拦截发布/订阅的消息以执行推送等操作，就像往队列里推送事件一样。

事件类型

键空间通知的实现是为每一个影响Redis数据空间的操作发送两个不同类型的事件。例如，在数据库0中名为mykey的键上执行DEL操作，将触发两条消息的传递，完全等同于下面两个PUBLISH命令：

PUBLISH __keyspace@0__:mykey del
PUBLISH __keyevent@0__:del mykey

以上很容易看到，一个频道允许监听所有以键mykey为目标的所有事件，以及另一个频道允许获取有关所有DEL操作目标键的信息。第一种事件，在频道中使用keyspace前缀的被叫做键空间通知，第二种，使用keyevent前缀的，被叫做键事件通知。在以上例子中，为键mykey生成了一个del事件。 会发生什么：

键空间频道接收到的消息是事件的名称。
键事件频道接收到的消息是键的名称。

可以只启用其中一种通知，以便只传递我们感兴趣的事件子集。

配置

默认情况下，键空间事件通知是不启用的，因为虽然不太明智，但该功能会消耗一些CPU。可以使用redis.conf中的notify-keyspace-events或者使用CONFIG SET命令来开启通知。将参数设置为空字符串会禁用通知。 为了开启通知功能，使用了一个非空字符串，由多个字符组成，每一个字符都有其特殊的含义，具体参见下表：

K     键空间事件，以__keyspace@<db>__前缀发布。
E     键事件事件，以__keyevent@<db>__前缀发布。
g     通用命令（非类型特定），如DEL，EXPIRE，RENAME等等
$     字符串命令
l     列表命令
s     集合命令
h     哈希命令
z     有序集合命令
x     过期事件（每次键到期时生成的事件）
e     被驱逐的事件（当一个键由于达到最大内存而被驱逐时产生的事件）
A     g$lshzxe的别名，因此字符串AKE表示所有的事件。

字符串中应当至少存在K或者E，否则将不会传递事件，不管字符串中其余部分是什么。例如，要为列表开启键空间事件，则配置参数必须设置为Kl，以此类推。字符串KEA可以用于开启所有可能的事件。

不同的命令生成的事件

根据以下列表，不同的命令产生不同种类的事件。

DEL命令为每一个删除的key生成一个del事件。
RENAME生成两个事件，一个是为源key生成的rename_from事件，一个是为目标key生成的rename_to事件。
EXPIRE在给一个键设置有效期时，会生成一个expire事件，或者每当设置有效期导致键被删除时，生成expired事件（请查阅EXPIRE文档以获取更多信息）。
SORT会在使用STORE选项将结果存储到新键时，生成一个sortstore事件。如果结果列表为空，且使用了STORE选项，并且已经存在具有该名称的键时，那个键将被删除，因此在这种场景下会生成一个del事件。
SET以及所有其变种（SETEX，SETNX，GETSET）生成set事件。但是SETEX还会生成一个expire事件。
MSET为每一个key生成一个set事件。
SETRANGE生成一个setrange事件。
INCR、DECR、INCRBY、DECRBY命令都生成incrby事件。
INCRBYFLOAT生成一个incrbyfloat事件。
APPEND生成一个append事件。
LPUSH和LPUSHX生成一个lpush事件，即使在可变参数情况下也是如此。
RPUSH和RPUSHX生成一个rpush事件，即使在可变参数情况下也是如此。
RPOP生成rpop事件。此外，如果键由于列表中的最后一个元素弹出而被删除，则会生成一个del事件。
LPOP生成lpop事件。此外，如果键由于列表中的最后一个元素弹出而被删除，则会生成一个del事件。
LINSERT生成一个linsert事件。
LSET生成一个lset事件。
LTRIM生成ltrim事件，此外，如果结果列表为空或者键被移除，将会生成一个del事件。
RPOPLPUSH和BRPOPLPUSH生成rpop事件和lpush事件。这两种情况下，顺序都将得到保证（lpush事件将总是在rpop事件之后传递）。此外，如果结果列表长度为零且键被删除，则会生成一个del事件。
HSET、HSETNX以及HMSET都生成一个hset事件。
HINCRBY生成一个hincrby事件。
HINCRBYFLOAT生成一个hincrbyfloat事件。
HDEL生成一个hdel事件，此外，如果结果哈希集为空或者键被移除，将生成一个del事件。
SADD生成一个sadd事件，即使在可变参数情况下也是如此。
SREM生成一个srem事件，此外，如果结果集合为空或者键被移除，将生成一个del事件。
SMOVE为每一个源key生成一个srem事件，以及为每一个目标key生成一个sadd事件。
SPOP生成一个spop事件，此外，如果结果集合为空或者键被移除，将生成一个del事件。
SINTERSTORE、SUNIONSTORE、SDIFFSTORE分别生成sinterstore、sunionostore、sdiffstore事件。在特殊情况下，结果集是空的，并且存储结果的键已经存在，因为删除了键，所以会生成del事件。
ZINCR生成一个zincr事件。
ZADD生成一个zadd事件，即使添加了多个元素。
ZREM生成一个zrem事件，即使删除了多个元素。当结果有序集合为空且生成了键，则会生成额外的del事件。
ZREMBYSCORE生成一个zrembyscore事件。当结果有序集合为空且生成了键，则会生成额外的del事件。
ZREMBYRANK生成一个zrembyrank事件。当结果有序集合为空且生成了键，则会生成额外的del事件。
ZINTERSTORE和ZUNIONSTORE分别生成zinterstore和zunionstore事件。在特殊情况下，结果有序集合是空的，并且存储结果的键已经存在，因为删除了键，所以会生成del事件。
每次一个拥有过期时间的键由于过期而从数据集中移除时，将生成一个expired事件。
每次一个键由于maxmemory策略而被从数据集中驱逐，以便释放内存时，将生成一个evicted事件。

重要 所有命令仅在真正修改目标键时才生成事件。例如，使用SREM命令从集合中删除一个不存在的元素将不会改变键的值，因此不会生成任何事件。如果对某个命令如何生成事件有疑问，最简单的方法是自己观察：

$ redis-cli config set notify-keyspace-events KEA
$ redis-cli --csv psubscribe '__key*__:*'
Reading messages... (press Ctrl-C to quit)
"psubscribe","__key*__:*",1

此时，在另外一个终端使用redis-cli发送命令到Redis服务器，并观察生成的事件：

"pmessage","__key*__:*","__keyspace@0__:foo","set"
"pmessage","__key*__:*","__keyevent@0__:set","foo"
...

过期事件的时间安排

设置了生存时间的键由Redis以两种方式过期：

当命令访问键时，发现键已过期。
通过后台系统在后台逐步查找过期的键，以便能够收集那些从未被访问的键。

当通过以上系统之一访问键且发现键已经过期时，将生成expired事件。因此无法保证Redis服务器在键过期的那一刻同时生成expired事件。如果没有命令不断地访问键，并且有很多键都有关联的TTL，那么在键的生存时间降至零到生成expired事件之间，将会有明显的延迟。基本上，expired事件是在Redis服务器删除键的时候生成的，而不是在理论上生存时间达到零值时生成的。
