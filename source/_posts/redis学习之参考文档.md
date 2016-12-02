---
title: redis学习之参考文档
comments: false
date: 2016-12-02 10:21:38
tags:
  - redis
categories:
  - 读书笔记
  - redis官方文档
---

# 1. 认识redis
　　redis是一个key-value的存储系统。它支持网络、可基于内存亦可持久化的日志型、key-value数据库。value值可支持string,map,list,sets和sorted sets等类型。
　　详细的说，redis相对其它key-value缓存产品(如memcached)有三个特点：
* 支持持久化，可将内存中的数据保持到磁盘中，重启时可再次加载进行使用。
* 不仅仅支持简单的key-value类型的数据，同事还提供list,set,zset,hash等数据结构的存储
* 支持数据备份,master-slave模式


  除此之外，redis还具备一下特点:
* 极高的性能：　读110000次/s，　写的速度81000次/s
* 丰富的数据类型
* 原子性：所有操作都是原子性的。
* 丰富的特性: 支持通知、key过期，publish/subscribe等

# 2.使用redis
　　对于一个工具，我们更关心的是如何使用。这里我从官方的文档中摘录出一些我自己比较感性去的部分罗列与此。和大家分享。
## 2.1　配置Redis
　　如何正确的使用配置文件，有利于我们更好的使用redis。这里我们认识一下常用命令。我安装了redis-cli，这里直接使用。
```bash
lennybai@lennybai:~$ redis-cli
127.0.0.1:6379> CONFIG GET loglevel
1) "loglevel"
2) "notice"
127.0.0.1:6379> config get *
  1) "dbfilename"
  2) "dump.rdb"
  3) "requirepass"
  4) ""
  5) "masterauth"
  6) ""
  7) "unixsocket"
  8) ""
  9) "logfile"
 10) "/var/log/redis/redis-server.log"
 11) "pidfile"
..........后面省略
```
　　我们可以通过config　get key获取配置文件中某项配置的值。并可以通过config set config_string_name new_config_value来更新其值。
　　下面对一些常用的配置项进行说明：


命令  |　说明  
--|--
daemonize  |  是否设为守护进程
pidfile  | 设置守护进程pid存储文件的位置
port | 指定redis端口(默认6379)
databases | 设置redis内置数据库的数量。通过select切换
save | 指定同步到文件的规则
maxclient | 最大客户连接数，0为不设限制
maxmemory | 最大分配内存

对于命令还有很多，大家可以在需要时自行查看。

## 2.2　redis数据类型
　　数据类型对于redis相对其他缓存产品而言，其丰富的数据格式是它的一大特色。这里做一下简单的介绍。

### string字符串类型

　　string类型是最基本的类型，它和memcached一样的类型.一个key对应一个value。redis中，string是二进制安全的，也就是说它可以存储任何数据(如图片或者序列化的对象)。一个键的最大存储为512MB.
```bash
redis 127.0.0.1:6379> SET name "redis.net.cn"
OK
redis 127.0.0.1:6379> GET name
"redis.net.cn"
```

### hash（哈希)
Redis hash是一个键值组合，它是一个string类型的field和value的映射表。特别适合存储对象。
```bash
redis 127.0.0.1:6379> HMSET user:1 username redis.net.cn password redis.net.cn points 200
OK
redis 127.0.0.1:6379> HGETALL user:1
1) "username"
2) "redis.net.cn"
3) "password"
4) "redis.net.cn"
5) "points"
6) "200"
```
每个 hash 可以存储 232 - 1 键值对（40多亿）。

### list(列表)
　　redis　list列表是简单的string类型列表。可以添加一个元素在列表的头部或者尾部。
```bash
redis 127.0.0.1:6379> lpush redis.net.cn redis
(integer) 1
redis 127.0.0.1:6379> lpush redis.net.cn mongodb
(integer) 2
redis 127.0.0.1:6379> lpush redis.net.cn rabitmq
(integer) 3
redis 127.0.0.1:6379> lrange redis.net.cn 0 10
1) "rabitmq"
2) "mongodb"
3) "redis"
redis 127.0.0.1:6379>
```
列表最多可存储 232 - 1 元素 (4294967295, 每个列表可存储40多亿)。

### set(集合)
　　Redis的set是string类型的无序集合。它是通过hash表实现的。增添和查找的复杂度为O(1).

> sadd key member

实例

```bash
redis 127.0.0.1:6379> sadd redis.net.cn redis
(integer) 1
redis 127.0.0.1:6379> sadd redis.net.cn mongodb
(integer) 1
redis 127.0.0.1:6379> sadd redis.net.cn rabitmq
(integer) 1
redis 127.0.0.1:6379> sadd redis.net.cn rabitmq
(integer) 0
redis 127.0.0.1:6379> smembers redis.net.cn

1) "rabitmq"
2) "mongodb"
3) "redis"
```
### zset（有序集合)
　　zset是一个有序的string集合，且不允许成员重复。每个成员会关联一个double类型的分数。通过分数进行排序。成员唯一，但是分数可以重复。
> zadd key score member

实例

```bash
redis 127.0.0.1:6379> zadd redis.net.cn 0 redis
(integer) 1
redis 127.0.0.1:6379> zadd redis.net.cn 0 mongodb
(integer) 1
redis 127.0.0.1:6379> zadd redis.net.cn 0 rabitmq
(integer) 1
redis 127.0.0.1:6379> zadd redis.net.cn 0 rabitmq
(integer) 0
redis 127.0.0.1:6379> ZRANGEBYSCORE redis.net.cn 0 1000

1) "redis"
2) "mongodb"
3) "rabitmq"
```

## 2.3 Redis命令
  通过介绍redis的命令，了解redis的使用
### PING
　　redis可以通过ping命令检测redis的运行情况。若正常运行，则返回pung
```bash
redis 127.0.0.1:6379> PING

PONG
```

### key相关命令
命令　|　说明
-- | --
Set key | 插入一个key-value
del key | 删除一个key-value.正确返回１，错误返回０
dump key | 序列化key。正确返回序列化后字符串，错误返回nil
exists key ｜　检测键值是否存在
expire key seconds | 设置键值过期时间，时间到了自动删除
move key db ｜　将当前数据库的key移动到其他数据库
persist key | 移除过期时间，永久保存
rename key new key | 重命名key
type key | 返回key的类型

### publish\subscribe命令
　　pub\sub是一个消息通信模式。通过subscribe订阅消息，然后通过publish发送消息。
```bash
#订阅者
redis 127.0.0.1:6379> SUBSCRIBE redisChat

Reading messages... (press Ctrl-C to quit)
1) "subscribe"
2) "redisChat"
3) (integer) 1
```

```bash
#发送者
redis 127.0.0.1:6379> PUBLISH redisChat "Redis is a great caching technique"

(integer) 1

redis 127.0.0.1:6379> PUBLISH redisChat "Learn redis by w3cschool.cc"

(integer) 1

# 订阅者的客户端会显示如下消息
1) "message"
2) "redisChat"
3) "Redis is a great caching technique"
1) "message"
2) "redisChat"
3) "Learn redis by w3cschool.cc"
```
　　通过unsubscribe退订频道。

### 事务
　　Redis 事务可以一次执行多个命令， 并且带有以下两个重要的保证：

* 事务是一个单独的隔离操作：事务中的所有命令都会序列化、按顺序地执行。事务在执行的过程中，不会被其他客户端发送来的命令请求所打断。
* 事务是一个原子操作：事务中的命令要么全部被执行，要么全部都不执行。


一个事务从开始到执行会经历以下三个阶段：
* 开始事务。
* 命令入队。
* 执行事务。

```bash
redis 127.0.0.1:6379> MULTI
OK

redis 127.0.0.1:6379> SET book-name "Mastering C++ in 21 days"
QUEUED

redis 127.0.0.1:6379> GET book-name
QUEUED

redis 127.0.0.1:6379> SADD tag "C++" "Programming" "Mastering Series"
QUEUED

redis 127.0.0.1:6379> SMEMBERS tag
QUEUED

redis 127.0.0.1:6379> EXEC
1) OK
2) "Mastering C++ in 21 days"
3) (integer) 3
4) 1) "Mastering Series"
   2) "C++"
   3) "Programming"
```

  discard取消事务执行。

### INFO　
查看当前redis所有信息

### 脚本支持
　 redis内置lua解析器

# 3 参考文献

- [1]: [redis官方文档](http://www.redis.net.cn/tutorial/3501.html)
