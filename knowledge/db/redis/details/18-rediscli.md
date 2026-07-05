---
title: "Redis-cli"
source: "https://redis.com.cn/topics/rediscli.html"
version: "7.x"
---

# Redis-cli

> 原始文档来源：https://redis.com.cn/topics/rediscli.html

---

redis-cli，Redis命令行工具

redis-cli是Redis命令行工具，是一个命令行客户端程序，可以将命令直接发送到Redis，并直接从终端读取服务器返回的应答。

它有两种主要模式：

交互模式：其中存在一个REPL（Read Eval Print Loop），用户可以在其中键入命令并获得答复；

参数模式：将命令作为redis-cli的参数发送，并打印执行结果在标准输出上。

redis-cli 可以使用一些选项来启动程序，以使其进入特殊模式，以完成更复杂的任务。

例如，

模拟从服务器并打印从主服务器接收的复制流，

检查Redis的延迟并打印统计数据，

显示延迟样本和频率以及其他许多东西的ASCII图。

本文将介绍redis-cli使用的方方面面，从最简单的内容开始入手，由浅入深。

*命令行用法

直接运行命令并将返回结果打印在标准输出上：

$ redis-cli incr mycounter
(integer) 7

该命令的返回值是“ 7”。Redis有多种返回值类型（可以是字符串，数组，整数，NULL，错误等），返回值中括号部分内容表示返回值的类型。

当将redis-cli的输出用作另一个命令的输入时，或者当我们要将其重定向到文件中时，这个返回致类型并不是我们想要的。

实际上，redis-cli仅当它检测到标准输出是tty（基本上是终端）时，才会显示其他信息，提高可读性。其它情况下，它将自动启用原始输出模式，不带返回值类型。如以下示例所示：

$ redis-cli incr mycounter > /tmp/output.txt
$ cat /tmp/output.txt
8

(integer)由于CLI检测到输出不再写入终端，因此从输出中省略了类型。可以使用--raw选项在终端上强制进行原始输出：

$ redis-cli --raw incr mycounter
9

同样，可以使用来在写入文件或通过管道将其传递给其他命令时强制使可读的输出--no-raw。

*主机，端口，密码和数据库

默认情况下redis-cli通过127.0.0.1端口6379连接到服务器。要指定其他主机名或IP地址，请使用-h。设置其他端口，请使用-p。

$ redis-cli -h redis15.localnet.org -p 6390 ping
PONG

如果您的实例受密码保护，则该-a <password>选项将执行身份验证，从而无需使用 AUTH 命令来明确地进行以下操作：

$ redis-cli -a myUnguessablePazzzzzword123 ping
PONG

或者，可以通过设置REDISCLI_AUTH环境变量提供密码。

最后，默认的数据库编号为零，可以使用-n <dbnum>选折特定编号的数据库：

$ redis-cli flushall
OK
$ redis-cli -n 1 incr a
(integer) 1
$ redis-cli -n 1 incr a
(integer) 2
$ redis-cli -n 2 incr a
(integer) 1

也可以使用-u <uri>：

$ redis-cli -u redis://p%40ssw0rd@redis-16379.hosted.com:16379/0 ping
PONG

*SSL / TLS

默认情况下，redis-cli使用明文TCP连接来连接到Redis。可以使用--tls选项启用SSL / TLS ，使用--cacert或
--cacertdir配置受信任的根证书捆绑包或目录。

如果目标服务器需要使用客户端证书进行身份验证，则可以使用--cert和 --key
来指定证书和相应的私钥。

*从其他程序获取输入

从stdin读取的输入做为redis-cli的最后一个参数。例如，读取文件/etc/services做为foo变量的值，使用-x
选项：

$ redis-cli -x set foo < /etc/services
OK
$ redis-cli getrange foo 0 50
"#\n# Network services, Internet style\n#\n# Note that "

上面的例子中，没指定 SET 命令的最后一个参数，但使用了-x选项，并将文件重定向到CLI的标准输入。因此，输入被读取，并用作命令的最后一个参数。

另一种方法是把redis-cli要执行的一系列写入文本文件：

$ cat /tmp/commands.txt
set foo 100
incr foo
append foo xxx
get foo
$ cat /tmp/commands.txt | redis-cli
OK
(integer) 101
(integer) 6
"101xxx"

依次执行commands.txt中的所有命令，就好像它们是由用户交互键入的一样。如果需要，可以在文件内用引号，以便可以在其中包含带空格或换行符的单个参数或其他特殊字符：

$ cat /tmp/commands.txt
set foo "This is a single argument"
strlen foo
$ cat /tmp/commands.txt | redis-cli
OK
(integer) 25

*连续运行相同的命令

当我们要连续监视一些关键内容或 INFO 字段输出时，或者当我们要模拟一些重复的写事件时（例如每5秒将一个新项目推入一个列表）。连续运行相同命令的功能很重要。

此功能由两个选项控制：-r <count>和-i <delay>。

第一个参数是运行命令的次数，第二个参数配置命令调用之间的延迟（以秒为单位）（十进制数（如0.1，以表示100毫秒）。

默认情况下，间隔（或延迟）设置为0，因此命令会立刻执行：

$ redis-cli -r 5 incr foo
(integer) 1
(integer) 2
(integer) 3
(integer) 4
(integer) 5

要永久运行同一命令，请使用-1做为 count。例如，监视RSS内存随时间的变化，可以使用如下命令：

$ redis-cli -r -1 -i 1 INFO | grep rss_human
used_memory_rss_human:1.38M
used_memory_rss_human:1.38M
used_memory_rss_human:1.38M
... a new line will be printed each second ...

*使用redis-cli插入大量数据

使用redis-cli插入大量数据会单独重点介绍，请参考
大量插入指南。

*CSV输出

有时您可能需要使用redis-cli快速将数据从Redis导出到外部程序。这可以使用CSV（逗号分隔值）输出功能来完成：

$ redis-cli lpush mylist a b c d
(integer) 4
$ redis-cli --csv lrange mylist 0 -1
"d","c","b","a"

目前，不可能像这样导出整个数据库，而只能运行带有CSV输出的单个命令。

*运行Lua脚本

redis-cli与以交互方式将脚本键入外壳程序或作为参数输入相比，您也可以使用一种更舒适的方式来运行文件中的脚本：

$ cat /tmp/script.lua
return redis.call('set',KEYS[1],ARGV[1])
$ redis-cli --eval /tmp/script.lua foo , bar
OK

Redis EVAL 命令将脚本使用的键列表以及其他非键参数作为不同的数组。使用EVAL时，需要将键的数量提供为数字。但是，redis-cli使用上述--eval选项，无需显式指定键的数量。相反，它使用以逗号分隔键和参数的约定。这就是为什么在上述调用中您将其foo , bar视为参数的原因。

因此foo将填充 KEYS 数组和bar该ARGV数组。

该--eval选项在编写简单脚本时很有用。对于更复杂的工作，使用Lua调试器绝对更舒适。可以混合使用两种方法，因为调试器还使用来自外部文件的执行脚本。

*互动模式

在交互模式下，用户在提示符下键入Redis命令。该命令将发送到服务器，进行处理，然后将回复解析并呈现为更简单的形式以供阅读。

运行CLI不需要任何特殊操作-无需任何参数即可启动它，即可处于交互模式下：

$ redis-cli
127.0.0.1:6379> ping
PONG

该字符串127.0.0.1:6379>是提示。它提醒您已连接到给定的Redis实例。

当您连接的服务器发生更改时，或者在数据库编号为零以外的数据库上运行时，提示也会更改：

127.0.0.1:6379> select 2
OK
127.0.0.1:6379[2]> dbsize
(integer) 1
127.0.0.1:6379[2]> select 0
OK
127.0.0.1:6379> dbsize
(integer) 503

*处理连接和重新连接

connect通过指定我们要连接的主机名和端口，以交互方式使用该命令可以连接到其他实例：

127.0.0.1:6379> connect metal 6379
metal:6379> ping
PONG

如您所见，提示会相应更改。如果用户尝试连接到无法访问的实例，则redis-cli进入断开连接模式，输入新命令时尝试重新连接：

127.0.0.1:6379> connect 127.0.0.1 9999
Could not connect to Redis at 127.0.0.1:9999: Connection refused
not connected> ping
Could not connect to Redis at 127.0.0.1:9999: Connection refused
not connected> ping
Could not connect to Redis at 127.0.0.1:9999: Connection refused

通常，在检测到断开连接之后，CLI总是尝试透明地重新连接：如果尝试失败，它将显示错误并进入断开连接状态。以下是断开连接和重新连接的示例：

127.0.0.1:6379> debug restart
Could not connect to Redis at 127.0.0.1:6379: Connection refused
not connected> ping
PONG
127.0.0.1:6379> (now we are connected again)

执行重新连接后，将redis-cli自动重新选择最后选择的数据库编号。但是，有关连接的所有其他状态都会丢失，例如如果处于事务状态中间，将会丢失事物状态：

$ redis-cli
127.0.0.1:6379> multi
OK
127.0.0.1:6379> ping
QUEUED

( here the server is manually restarted )

127.0.0.1:6379> exec
(error) ERR EXEC without MULTI

在交互式模式下使用CLI进行测试时，通常这不是问题，但是您应该意识到这一限制。

*编辑，历史和自动补全

因为redis-cli使用
linenoise，所以它始终具有行编辑功能，而无需依赖libreadline或其他可选库。

您可以访问已执行命令的历史记录，以免反复按箭头键（上和下）来再次键入它们。历史记录在两次CLI重新启动之间保留在.rediscli_history，该文件位于用户主目录内。可以通过设置REDISCLI_HISTFILE环境变量来使用其他历史记录文件名，并通过将其设置为/dev/null来禁用它。

CLI也可以通过按TAB键来完成命令名称，如以下示例所示：

127.0.0.1:6379> Z<TAB>
127.0.0.1:6379> ZADD<TAB>
127.0.0.1:6379> ZCARD<TAB>

*N次执行相同的命令

通过在命令名称前加上数字前缀，可以多次运行同一命令：

127.0.0.1:6379> 5 incr mycounter
(integer) 1
(integer) 2
(integer) 3
(integer) 4
(integer) 5

*显示有关Redis命令的帮助

Redis有许多命令，有时在测试时，您可能不记得参数的确切顺序。redis-cli使用help命令为大多数Redis命令提供联机帮助。该命令可以两种形式使用：

help @<category>显示给定类别的所有命令。类别有：@generic，@list，@set，@sorted_set，@hash，
@pubsub，@transactions，@connection，@server，@scripting，
@hyperloglog。
help <commandname> 显示特定命令的帮助。

例如，为了显示 PFADD 命令的帮助，请使用：

127.0.0.1:6379>help PFADD

PFADD key element [element …] summary: Adds the specified elements to the specified HyperLogLog. since: 2.8.9

请注意，help支持TAB补全。

*清除终端屏幕

在交互模式下，使用clear命令将清除终端的屏幕。

*特殊的操作模式

到目前为止，我们已经看到了两种主要的redis-cli使用模式。

Redis命令的命令行执行。
交互式“ REPL”用法。

下面是CLI执行与Redis相关的其他辅助任务。

*连续统计模式

这可能是的鲜为人知的功能之一，并且对于实时监视Redis实例非常有用。要启用此模式，请使用--stat选项：

$ redis-cli --stat
------- data ------ --------------------- load -------------------- - child -
keys       mem      clients blocked requests            connections
506        1015.00K 1       0       24 (+0)             7
506        1015.00K 1       0       25 (+1)             7
506        3.40M    51      0       60461 (+60436)      57
506        3.40M    51      0       146425 (+85964)     107
507        3.40M    51      0       233844 (+87419)     157
507        3.40M    51      0       321715 (+87871)     207
508        3.40M    51      0       408642 (+86927)     257
508        3.40M    51      0       497038 (+88396)     257

在这种模式下，每秒钟都会打印一条新行，其中包含有用的信息以及与旧数据点之间的差异。您可以轻松地了解内存使用情况，连接的客户端等情况。

-i <interval>可修改更新频率，默认值为一秒钟。

*扫描大键

在这种特殊模式下，redis-cli充当键空间分析器。它会在数据集中扫描大键，但还会提供有关数据集所包含的数据类型的信息。该模式通过该--bigkeys选项启用，并产生非常详细的输出：

$ redis-cli --bigkeys

# Scanning the entire keyspace to find biggest keys as well as
# average sizes per key type.  You can use -i 0.1 to sleep 0.1 sec
# per 100 SCAN commands (not usually needed).

[00.00%] Biggest string found so far 'key-419' with 3 bytes
[05.14%] Biggest list   found so far 'mylist' with 100004 items
[35.77%] Biggest string found so far 'counter:__rand_int__' with 6 bytes
[73.91%] Biggest hash   found so far 'myobject' with 3 fields

-------- summary -------

Sampled 506 keys in the keyspace!
Total key length in bytes is 3452 (avg len 6.82)

Biggest string found 'counter:__rand_int__' has 6 bytes
Biggest   list found 'mylist' has 100004 items
Biggest   hash found 'myobject' has 3 fields

504 strings with 1403 bytes (99.60% of keys, avg size 2.78)
1 lists with 100004 items (00.20% of keys, avg size 100004.00)
0 sets with 0 members (00.00% of keys, avg size 0.00)
1 hashs with 3 fields (00.20% of keys, avg size 3.00)
0 zsets with 0 members (00.00% of keys, avg size 0.00)

在输出的第一部分中，报告遇到的每个比先前的较大键（相同类型）大的新键。摘要部分提供有关Redis实例内部数据的常规统计信息。

该程序使用 SCAN 命令，因此可以在繁忙的服务器上执行该命令而不会影响操作，但是-i可以使用该选项来限制所请求的每100个键的指定秒数的扫描过程。例如，这-i 0.1将大大减慢程序的执行速度，但也将服务器上的负载减少到很小的程度。

请注意，该摘要还以更简洁的形式报告了每次找到的最大密钥。如果针对非常大的数据集运行，则初始输出只是尽快提供一些有趣的信息。

*非阻塞获取所有键列表

获取redis的key空间，但是不会像KEYS *那样阻塞Redis。像--bigkeys选项一样，此模式使用 SCAN 命令，因此如果数据集正在更改，则可能会多次报告键，但是如果自迭代开始以来一直存在该键，则不会丢失任何键。

$ redis-cli --scan | head -10
key-419
key-71
key-236
key-50
key-38
key-458
key-453
key-499
key-446
key-371

head \-10命令用于打印输出的前10行。

扫描符合给定模式的key，--pattern。

$ redis-cli --scan --pattern '*-11*'
key-114
key-117
key-118
key-113
key-115
key-112
key-119
key-11
key-111
key-110
key-116

配合wc统计特定种类的key个数：

$ redis-cli --scan --pattern 'user:*' | wc -l
3829433

*发布/订阅模式

CLI只需使用 PUBLISH 命令就可以在Redis发布/订阅通道中发布消息。因为 PUBLISH 命令与任何其他命令都非常相似，所以这是可以预期的。订阅频道以接收消息是不同的-在这种情况下，我们需要阻止并等待消息，因此在中将其实现为一种特殊模式redis-cli。与其他特殊模式不同，此模式不是通过使用特殊选项启用的，而只是通过使用 SUBSCRIBE 或 PSUBSCRIBE 命令以交互或非交互模式启用的：

$ redis-cli psubscribe '*'
Reading messages... (press Ctrl-C to quit)
1) "psubscribe"
2) "*"
3) (integer) 1

在阅读邮件消息显示，我们进入的Pub / Sub模式。当另一个客户端在某个通道中发布某些消息时（例如，您可以使用进行操作）redis-cli PUBLISH mychannel mymessage，发布/订阅模式下的CLI将显示以下内容：

1) "pmessage"
2) "*"
3) "mychannel"
4) "mymessage"

这对于调试发布/订阅问题非常有用。要退出发布/订阅模式，只需处理CTRL-C。

*监视在Redis中执行的命令

与发布/订阅模式相似，一旦使用了 MONITOR 模式，便会自动进入监视模式。它将打印Redis实例收到的所有命令：

$ redis-cli monitor
OK
1460100081.165665 [0 127.0.0.1:51706] "set" "foo" "bar"
1460100083.053365 [0 127.0.0.1:51707] "get" "foo"

请注意，可以使用管道传递输出，因此您可以使用诸如grep之类的工具监视特定的模式。

*监控Redis实例的延迟

Redis通常用于延迟非常关键的环境中。延迟涉及应用程序中的多个移动部分，从客户端库到网络堆栈，再到Redis实例本身。

CLI具有多种功能，可用于研究Redis实例的延迟并了解延迟的最大值，平均值和分布。

基本延迟检查工具是--latency可选的。CLI使用此选项运行一个循环，在该循环中，将 PING 命令发送到Redis实例，并测量获得回复的时间。每秒发生100次，并且统计信息会在控制台中实时更新：

$ redis-cli --latency
min: 0, max: 1, avg: 0.19 (427 samples)

统计信息以毫秒为单位。通常，非常快的实例的平均延迟往往由于系统redis-cli
自身的内核调度程序而导致的延迟而被高估了一点，因此，平均延迟为0.19以上可能很容易为0.01或更小。但是，这通常不是什么大问题，因为我们对几毫秒或更长时间的事件感兴趣。

有时，研究最大和平均延迟如何随时间变化是有用的。该--latency-history选项用于此目的：它的工作方式与完全相同--latency，但是每15秒（默认情况下）从头开始一个新的采样会话：

$ redis-cli --latency-history
min: 0, max: 1, avg: 0.14 (1314 samples) -- 15.01 seconds range
min: 0, max: 1, avg: 0.18 (1299 samples) -- 15.00 seconds range
min: 0, max: 1, avg: 0.20 (113 samples)^C

您可以使用该-i <interval>选项更改采样会话的长度。

最先进的延迟研究工具，但对于没有经验的用户来说，也更难解释，它是使用彩色终端显示延迟频谱的能力。您将看到一个彩色输出，指示不同百分比的样本，以及不同的ASCII字符，指示不同的等待时间数字。使用以下--latency-dist
选项启用此模式：

$ redis-cli --latency-dist
(output not displayed, requires a color terminal, try it!)

内部还有另一个非常不寻常的延迟工具redis-cli。它不检查Redis实例的延迟，而是检查您所运行的计算机的延迟redis-cli。您可能会问什么延迟？内核调度程序，虚拟化实例情况下的管理程序固有的延迟等等。

之所以称其为固有延迟，是因为它对程序员来说是不透明的。如果您的Redis实例具有严重的延迟，而不管可能是引起原因的所有显而易见的事情，那么值得检查一下，通过redis-cli直接在运行Redis服务器的系统中以这种特殊模式运行，系统可以做的最好的事情。

通过测量固有延迟，您知道这是基线，Redis无法超越您的系统。为了在此模式下运行CLI，请使用--intrinsic-latency <test-time>。测试的时间以秒为单位，并指定redis-cli应检查多少秒才能检查当前正在运行的系统的延迟。

$ ./redis-cli --intrinsic-latency 5
Max latency so far: 1 microseconds.
Max latency so far: 7 microseconds.
Max latency so far: 9 microseconds.
Max latency so far: 11 microseconds.
Max latency so far: 13 microseconds.
Max latency so far: 15 microseconds.
Max latency so far: 34 microseconds.
Max latency so far: 82 microseconds.
Max latency so far: 586 microseconds.
Max latency so far: 739 microseconds.

65433042 total runs (avg latency: 0.0764 microseconds / 764.14 nanoseconds per run).
Worst run took 9671x longer than the average latency.

重要说明：此命令必须在要在其上运行Redis服务器的计算机上执行，而不是在其他主机上。它甚至不连接到Redis实例，而仅在本地执行测试。

在上述情况下，我的系统无法完成比最坏情况下的739微秒更好的延迟，因此我可以预期某些查询有时会在不到1毫秒的时间内运行。

*RDB文件的远程备份

在Redis复制的第一次同步过程中，主服务器和从服务器以RDB文件的形式交换整个数据集。redis-cli为了提供远程备份功能，可以利用此功能，该功能允许将RDB文件从任何Redis实例传输到运行的本地计算机
redis-cli。要使用此模式，请使用以下--rdb <dest-filename>
选项调用CLI ：

$ redis-cli --rdb /tmp/dump.rdb
SYNC sent to master, writing 13256 bytes to '/tmp/dump.rdb'
Transfer finished with success.

这是确保您的Redis实例具有灾难恢复RDB备份的简单但有效的方法。但是，在脚本或cron作业中使用此选项时，请确保检查命令的返回值。如果它不为零，则发生错误，如以下示例所示：

$ redis-cli --rdb /tmp/dump.rdb
SYNC with master failed: -ERR Can't SYNC while not connected with my master
$ echo $?
1

*从机模式

CLI的从模式是一项高级功能，对Redis开发人员和调试操作很有用。它允许检查主服务器在复制流中发送给其从服务器的内容，以便将写入传播到其副本。选项名称就是--slave。它是这样工作的：

$ redis-cli --slave
SYNC with master, discarding 13256 bytes of bulk transfer...
SYNC done. Logging commands from master.
"PING"
"SELECT","0"
"set","foo","bar"
"PING"
"incr","mycounter"

该命令首先丢弃第一次同步的RDB文件，然后将接收到的每个命令记录为CSV格式。

如果您认为某些命令没有在从属服务器中正确复制，则这是检查正在发生的事情的好方法，同时也是改善缺陷报告的有用信息。

*执行LRU模拟

Redis通常用作具有LRU驱逐功能的缓存。根据键的数量和为高速缓存分配的内存量（通过maxmemory指令指定），高速缓存的命中和未命中量将发生变化。有时，模拟命中率对于正确配置缓存非常有用。

CLI具有一种特殊的模式，它在请求模式中使用80-20％的幂定律分布来模拟GET和SET操作。这意味着20％的密钥将被请求80％的时间，这是缓存方案中的常见分布。

从理论上讲，考虑到请求的分布和Redis内存开销，应该可以使用数学公式来分析计算命中率。但是，Redis可以配置为具有不同的LRU设置（样本数），并且在Redis中近似的LRU的实现在不同版本之间会发生很大变化。同样，每个密钥的内存量可能会在版本之间变化。这就是构建该工具的原因：其主要目的是测试Redis LRU实施的质量，但现在也可用于测试给定版本在部署时考虑的设置下的行为。

为了使用此模式，您需要指定测试中的键数。您还需要配置一个适合maxmemory初次尝试的设置。

重要说明：maxmemory在Redis配置中配置设置至关重要：如果没有最大内存使用量的上限，则由于所有密钥都可以存储在内存中，因此命中率最终将为100％。或者，如果您指定的键太多而没有最大内存，则最终将使用所有计算机RAM。在大多数情况下，还需要配置适当的
最大内存策略allkeys-lru。

在以下示例中，我将内存限制配置为100MB，并使用1000万个键进行LRU模拟。

警告：该测试使用流水线操作，并且会对服务器造成压力，请勿将其用于生产实例。

$ ./redis-cli --lru-test 10000000
156000 Gets/sec | Hits: 4552 (2.92%) | Misses: 151448 (97.08%)
153750 Gets/sec | Hits: 12906 (8.39%) | Misses: 140844 (91.61%)
159250 Gets/sec | Hits: 21811 (13.70%) | Misses: 137439 (86.30%)
151000 Gets/sec | Hits: 27615 (18.29%) | Misses: 123385 (81.71%)
145000 Gets/sec | Hits: 32791 (22.61%) | Misses: 112209 (77.39%)
157750 Gets/sec | Hits: 42178 (26.74%) | Misses: 115572 (73.26%)
154500 Gets/sec | Hits: 47418 (30.69%) | Misses: 107082 (69.31%)
151250 Gets/sec | Hits: 51636 (34.14%) | Misses: 99614 (65.86%)

该程序每秒显示一次统计信息。如您所见，在最初的几秒钟内，缓存开始被填充。后来的未命中率稳定在很长一段时间内的实际数字中：

120750 Gets/sec | Hits: 48774 (40.39%) | Misses: 71976 (59.61%)
122500 Gets/sec | Hits: 49052 (40.04%) | Misses: 73448 (59.96%)
127000 Gets/sec | Hits: 50870 (40.06%) | Misses: 76130 (59.94%)
124250 Gets/sec | Hits: 50147 (40.36%) | Misses: 74103 (59.64%)

对于我们的用例来说，59％的错率可能是不可接受的。因此，我们知道100MB的内存不足。让我们尝试使用半GB。几分钟后，我们将看到输出稳定到以下数字：

140000 Gets/sec | Hits: 135376 (96.70%) | Misses: 4624 (3.30%)
141250 Gets/sec | Hits: 136523 (96.65%) | Misses: 4727 (3.35%)
140250 Gets/sec | Hits: 135457 (96.58%) | Misses: 4793 (3.42%)
140500 Gets/sec | Hits: 135947 (96.76%) | Misses: 4553 (3.24%)

因此，我们知道，有了500MB的存储空间，我们的密钥数量（1000万个）和分发数量（80-20种样式）已经足够了。
