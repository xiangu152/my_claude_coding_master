---
title: "PHP Redis 扩展"
source: "https://redis.com.cn/topics/php-redis-extension.html"
version: "7.x"
---

# PHP Redis 扩展

> 原始文档来源：https://redis.com.cn/topics/php-redis-extension.html

---

PHP Redis扩展
XAMPP配置PHP Redis扩展

1、使用phpinfo查看php版本

2、下载对应版本的php-redis模块
（1）下载扩展地址：https://github.com/phpredis/phpredis/downloads
（2）将下载的以下文件放到xampp/php/ext下。

 php_igbinary.dll
 php_redis.dll 

注意：下载时候，带-ts-的是线程安全的，带-nts-是非线程安全的。
（3）修改php.ini文件

extension=php_igbinary.dll
extension=php_redis.dll

注意：顺序不能换。

3、重启xampp中的apache模块，重新执行phpinfo即可看到redis模块。 

4、测试示例

$redis = new Redis();
//连接，php客户端设置的ip及端口  
$redis->connect("127.0.0.1","6379");
//存储一个值  
$redis->set("say","redis.com.cn");
//取值
echo $redis->get("say");

Linux环境配置PHP Redis扩展

PHP在使用Redis之前，必须确保已经安装了Redis服务以及PHP Redis驱动，以下是安装PHP Redis驱动的方法。也可以参考php 操作 redis
下载地址：https://github.com/owlient/phpredis
1、PHP安装redis扩展,进入phpredis源码目录执行以下命令

/usr/local/php/bin/phpize   #php安装后的路径
./configure --with-php-config=/usr/local/php/bin/php-config
make && make install

2、修改php.ini文件

vi /usr/local/php/lib/php.ini

增加如下内容：

extension_dir = "/usr/local/php/lib/php/extensions/no-debug-zts-20090626"
extension=redis.so

3、重启apache服务器 使用phpinfo就能查看到Redis扩展。
