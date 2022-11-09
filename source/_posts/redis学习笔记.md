---
title: redis学习笔记
date: 2022-10-04 23:00:23
categories: 后端
tags: [数据库, noSQL, redis]
---

# Redis学习笔记

## (1)、什么是redis?

​	REmote DIctionary Server(Redis) 是一个由 Salvatore Sanfilippo 写的 key-value 存储系统，是跨平台的非关系型数据库。Redis 是一个开源的使用 ANSI C 语言编写、遵守 BSD 协议、支持网络、可基于内存、分布式、可选持久性的键值对(Key-Value)存储数据库，并提供多种语言的 API。Redis 通常被称为数据结构服务器，因为值（value）可以是**字符串(String)**、**哈希(Hash)**、**列表(list)**、**集合(sets)**和**有序集合(sorted sets)**等类型。

## (2)、使用

### 1、常见指令

- **keys** ***** :查看当前库所有的key,或者匹配某个key(key *key1)
- **set key value**: 设置值
- **exists key**: 判断某个key是否存在
- **type key**: 查看key的类型
- **del key**: 删除某个key
- **unlink key**: 根据value选择非阻塞删除
- **expire key second**: 设置key的过期时间，单位为秒
- **ttl key**: 查看某个key的状态，-1 永远过期，-2 已过期
- **select [0~15]**:  切换当前数据库，默认数据库为0,总有16个数据库
- **dbsize**: 查看数据库所有的key的总数
- **flushdb**: 清空当前数据库的key
- **flushall**: 清空所有库的key

### 2、数据类型

- #### String字符串

  **简介**: String是Redis的基本类型，是**二进制安全的**,可以包含任何数据，一个Redis中字符串value最多可以是**512M**

  **常用指令**:

  - **set <key> <value>**: 设置值
  - **get <key>**: 查找key的value
  - **append <key> <str>**: 在key的值后面追加字符串
  - **strlen <key>**: 获取key值的长度
  - **setnx <key> <value>**: 和set一样都是设置值，当时setnx只有当key不存在时,才会设置key的值
  - **incr <key>**: 设置key的值+1
  - **decr <key>**: 设置key的值-1
  - **incrby <key> <步长>**: 设置key的值+步长值
  - **decrby <key> <步长>**: 设置key的值-步长值  
  - 注: 以上针对数值的加减操作只针对数字，加字符无效，而且这些都属于原子操作,(原子操作: 指的是不会被线程调度机制打断的操作，这种操作一旦开始，就会一直运行到结束，中间不会有任何context switch),Redis单线程的原子性主要得益于Redis的单线程。
  - **mset <k1> <v1> <k2> <v2>**: 设置多个key的值
  - **mget <k1> <k2> <k3>**: 获取多个key的值
  - **msetnx  <k1> <v1> <k2> <v2>** : 设置多个值，作用和setnx相同
  - **getrange <key> <start> <end>**: 截取字符串指定下标的值，类似于js的strstring
  - **setex <key> <timer>  <value>:** 和expire一样，但是setex是同时设置值和过期时间的 
  - **getset <key> <value>**:获取值的时候重置值

- #### List列表

  **简介**: List列表类似于JS的数组结构，他是一个双链表的结构，可以从头遍历，也可以从尾部遍历

  **常用指令**:

  - **rpush <k> <v1> <k2> <k3>**: 将值从右往左推入，结果为:[v1、k2、k3]

  - **lpush <k> <v1> <v2> <v3>**: 将值从左到右推入,结果为:[v3、v2、v1] 

  - **lpop <k>**: 将左边的第一个值推出

  - **rpop <k>**: 将右边的第一个值推出

    注: 当一个key的所有值都被推出时，该key就会默认失效

  - **lrange <k> <start> <end>**: 取出下标为start和end之间的值，当start为0，end为-1时，默认取全部

  - **lindex <k> <index>**: 取对应下标的值，默认从0开始

  - **llen <k>**: 获得列表的长度

  - **rpoplpush <k1> <k2>**: 将列表k1最右边的值推出,并且将该值从左到右推入列表k2

  - **linsert <k> before/after <v> <newV>**: 在v之前/之后插入新的值

  - **lrem <k> <n> <v>**: 删除列表的n个v值

  - **lset <k> <index> <v>**: 将下标为index的值替换成v

- #### Set列表

  **简介**: Set列表合List列表相似，但是Set具有自动排重的功能,底层结构是dict字典，是用哈希表实现的

  **常用指令**:

  - **sadd <k> <v1> <v2>**: 新建一个set列表
  - **smembers <k>**: 查看一个列表的所有值
  - **sismembers <k> <v>**: 判断值是否在列表中存在，存在返回1，不存在返回0
  - **spop <k>**: 随机从列表中弹出一个值
  - **srem <k> <v>**: 删除列表的某个值
  - **srandmember <k> <n>**: 随机在列表中取出n个值
  - **smove <k1> <k2> <v>**: 将k1中的v移动到k2
  - **sinter <k1> <k2>**: 取k1、k2的交集
  - **sunion <k1> <k2>**: 取k1、k2的并集
  - **sdiff <k1> <k2>**: 取k1、k2的差集

- #### Hash集合

  **简介**: 类似于JS的MAP结构，是按照field和value，键值对映射的关系

  **常用指令**:

  - **hset <k> <field> <value>**:新建一个hash集合,或者是往已有的集合中添加field-value
  - **hget <k> <field>**: 取集合中对应field的值
  - **hmset <k> <field> <value> <field> <value>**: 可以为一个集合中添加多个field-value
  - **hexists <k> <field>**：判断集合中的field是否存在
  - **hkeys <k>**: 列出集合的所有field
  - **hvals <k>**: 列出集合的所有value
  - **hincrby <key> <field> <num> **: 为集合特定的field对应的值+num
  - **hsetnx <key> <field> <value>**: 为集合设置field为value的键值对，当且仅当field不存在时，才会生效

- **ZSet有序集合**

  **简介**: 和Set类型一致，但是ZSet比较突出的一点的是，它是有序的，每个value都有一个分数，ZSet可以对分数进行排序

  **常用指令**:

  - **zadd <k> <scores> <v1> <scores> <v2>**: 新建一个Zset集合，其中scores是每个v的分数，无论天添加的先后顺序，都是安装scores按照从小到大排序的
  - **zrange <k> <start> <end> withscores**: 查看集合的范围，当start等于0，end等于-1时，默认取该集合的全部值，带上withscores，可以让分数和结果集一起返回
  - **zrangebyscore <k> <min> <max> withscores**: 查看集合min和max之间的值，带上withscores，可以让分数和结果集一起返回
  - **zrevrangebyscore <k> <max> <min> withscores**: 查看集合在max到min之间的值,和zrangebyscore区别的是，它是倒序的
  - **zincrby <k> <score> <v>**: 为v的分数增加score分
  - **zrem <k> <v>**: 删除集合的某个v
  - **zcount <k> <min> <max>**: 返回集合在min~max之间分数的值
  - zrank <k> <v>: 返回集合v所在的排名，从0开始

### 3、配置文件

```conf
# Redis configuration file example

# Note on units: when memory size is needed, it is possible to specify
# it in the usual form of 1k 5GB 4M and so forth:
#单位转换
# 1k => 1000 bytes
# 1kb => 1024 bytes
# 1m => 1000000 bytes
# 1mb => 1024*1024 bytes
# 1g => 1000000000 bytes
# 1gb => 1024*1024*1024 bytes
#
# units are case insensitive so 1GB 1Gb 1gB are all the same.

################################## INCLUDES ###################################

# Include one or more other config files here.  This is useful if you
# have a standard template that goes to all Redis servers but also need
# to customize a few per-server settings.  Include files can include
# other files, so use this wisely.
#
# Notice option "include" won't be rewritten by command "CONFIG REWRITE"
# from admin or Redis Sentinel. Since Redis always uses the last processed
# line as value of a configuration directive, you'd better put includes
# at the beginning of this file to avoid overwriting config change at runtime.
#
# If instead you are interested in using includes to override configuration
# options, it is better to use include as the last line.
#引入公共文件
# include .\path\to\local.conf
# include c:\path\to\other.conf

################################ GENERAL  #####################################

# On Windows, daemonize and pidfile are not supported.
# However, you can run redis as a Windows service, and specify a logfile.
# The logfile will contain the pid. 

# Accept connections on the specified port, default is 6379.
# If port 0 is specified Redis will not listen on a TCP socket.
port 6379//默认端口号

# TCP listen() backlog.
#
# In high requests-per-second environments you need an high backlog in order
# to avoid slow clients connections issues. Note that the Linux kernel
# will silently truncate it to the value of /proc/sys/net/core/somaxconn so
# make sure to raise both the value of somaxconn and tcp_max_syn_backlog
# in order to get the desired effect.
tcp-backlog 511 //连接队列，队列总和=未完成三次握手队列 + 已完成三次握手队列

# By default Redis listens for connections from all the network interfaces
# available on the server. It is possible to listen to just one or multiple
# interfaces using the "bind" configuration directive, followed by one or
# more IP addresses.
#
# Examples:
#
# bind 192.168.1.100 10.0.0.1
# bind 127.0.0.1


# Specify the path for the Unix socket that will be used to listen for
# incoming connections. There is no default, so Redis will not listen
# on a unix socket when not specified.
#
# unixsocket /tmp/redis.sock
# unixsocketperm 700

# Close the connection after a client is idle for N seconds (0 to disable)
timeout 0 //设置连接超时时间，0:用不产生

# TCP keepalive.
#
# If non-zero, use SO_KEEPALIVE to send TCP ACKs to clients in absence
# of communication. This is useful for two reasons:
#
# 1) Detect dead peers.
# 2) Take the connection alive from the point of view of network
#    equipment in the middle.
#
# On Linux, the specified value (in seconds) is the period used to send ACKs.
# Note that to close the connection the double of the time is needed.
# On other kernels the period depends on the kernel configuration.
#
# A reasonable value for this option is 60 seconds.
tcp-keepalive 0//检测连接周期，没过几秒检测一次

```

### **4、发布订阅**

​	在redis中可以通过订阅频道和发布来实现两个终端的通信

```
> subscribe k//订阅频道k-终端1
> publish k hello//在频道k中发布信息-终端2
> message //终端1
> k //终端1
> hello //终端1
```

