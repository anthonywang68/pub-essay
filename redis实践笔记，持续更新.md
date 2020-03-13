---
title: redis实践笔记，持续更新
date: 2019-09-20 22:24:09
tags:
---

补充具体步骤在我的博客园：以备参考；

https://www.cnblogs.com/anthony-wang0228/articles/11556585.html

### 概述

redis(REmote DIctionary Server)是一个由Salvatore Sanfilippo写key-value存储系统，它由C语言编写、遵守BSD协议、支持网络、可基于内存亦可持久化的日志型、Key-Value类型的数据库，并提供多种语言的API。

<!--more-->

和Memcached类似，它支持存储的value类型相对更多，包括string(字符串)、list(链表)、set(集合)、zset(sorted set --有序集合)和hash（哈希类型）。这些数据类型都支持push/pop、add/remove及取交集并集和差集及更丰富的操作，而且这些操作都是原子性的。在此基础上，redis支持各种不同方式的排序。与memcached一样，为了保证效率，数据都是缓存在内存中。区别的是redis会周期性的把更新的数据写入磁盘或者把修改操作写入追加的记录文件，并且在此基础上实现了master-slave(主从)同步，redis在3.0版本推出集群模式。

### 特点、优势

- k、v键值存储以及数据结构存储（如列表、字典）
- 所有数据(包括数据的存储)操作均在内存中完成
- 单线程服务(这意味着会有较多的阻塞情况)，采用epoll模型进行请求响应，对比nginx
- 支持主从复制模式，更提供高可用主从复制模式（哨兵）
- 去中心化分布式集群
- 丰富的编程接口支持，如Python、Golang、Java、php、Ruby、Lua、Node.js 
- 功能丰富，除了支持多种数据结构之外，还支持事务、发布/订阅、消息队列等功能
- 支持数据持久化(AOF、RDB)

### 对比memcache

- memcache是一个分布式的内存对象缓存系统，并不提供持久存储功能，而redis拥有持久化功能
- memcache数据存储基于LRU(简单说：最近、最少使用key会被剔除)，而redis则可以永久保存(服务一直运行情况下)
- memcache是多线程的（这是memcache优势之一），也就意味着阻塞情况少，而redis是单线程的，阻塞情况相对较多
- 两者性能上相差不大
- memcache只支持简单的k、v数据存储，而redis支持多种数据格式存储。
- memcache是多线程、非阻塞IO复用网络模型，而redis是单线程IO复用模型

## 二、开始

### 源码部署

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
yum install gcc -y  #安装C依赖
wget http://download.redis.io/redis-stable.tar.gz  #下载稳定版本
tar zxvf redis-stable.tar.gz  #解压
cd redis-stable
make PREFIX=/opt/app/redis install   #指定目录编译，也可以不用指定
make install
mkdir /etc/redis   #建立配置目录
cp redis.conf /etc/redis/6379.conf # 拷贝配置文件
cp utils/redis_init_script /etc/init.d/redis  #拷贝init启动脚本针对6.X系统
chmod a+x  /etc/init.d/redis  #添加执行权限
vi /etc/redis/6379.conf #修改配置文件： 
bind 0.0.0.0      #监听地址
maxmemory 4294967296   #限制最大内存（4G）：
daemonize yes   #后台运行

####启动与停止
/etc/init.d/redis start
/etc/init.d/redis stop
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

查看是否成功安装

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
#执行客户端工具
redis-cli 
#输入命令info
127.0.0.1:6379> info
# Server
redis_version:4.0.10
redis_git_sha1:00000000
redis_git_dirty:0
redis_build_id:cf83e9c690dbed33
redis_mode:standalone
os:Linux 2.6.32-642.el6.x86_64 x86_64
arch_bits:64
multiplexing_api:epoll
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

### 二进制文件说明

redis安装完成后会有以下可执行文件（window下是exe文件）生成，下面是各个文件的作用。

```
redis-server　　　　   #Redis服务器和Sentinel服务器，启动时候可使用--sentinel指定为哨兵
redis-cli　　　　　    #Redis命令行客户端 
redis-benchmark　     #Redis性能测试工具 
redis-check-aof      #AOF文件修复工具 
redis-check-dump     #RDB文件检测工具 
redis-sentinel       #Sentinel服务器,4.0版本已经做了软链接到redis-server
```

## 三、配置详解

redis所有的配置参数都可以通过客户端通过“CONFIG GET 参数名” 获取，参数名支持通配符，如*代表所有。所得结果并按照顺序分组，第一个返回结果是参数名，第二个结果是参数对应的值。

![img](https://images2018.cnblogs.com/blog/1075473/201807/1075473-20180719172522490-982020206.png)

除了查看配置还可以使用CONFIG SET修改配置，写入配置文件使用CONFIG REWRITE,使用时是需要注意某些关于服务配置参数慎重修改，如bind。

![img](https://images2018.cnblogs.com/blog/1075473/201807/1075473-20180719180427650-857052801.png)

配置参数以及解释，需要注意的是，有些配置是4.0.10新增的，有些配置已经废除，如vm相关配置，和集群相关配置在集群篇章在进行补充。

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
logfile
#日志文件位置及文件名称

bind 0.0.0.0
#监听地址，可以有多个 如bind 0.0.0.0 127.0.0.1

daemonize yes
#yes启动守护进程运行，即后台运行，no表示不启用

pidfile /var/run/redis.pid 
# 当redis在后台运行的时候，Redis默认会把pid文件在在/var/run/redis.pid，也可以配置到其他地方。
# 当运行多个redis服务时，需要指定不同的pid文件和端口

port 6379
# 指定redis运行的端口，默认是6379

unixsocket 
#sock文件位置

unixsocketperm
#sock文件权限

timeout 0
# 设置客户端连接时的超时时间，单位为秒。当客户端在这段时间内没有发出任何指令，那么关闭该连接， 0是关闭此设置

loglevel debug
# 指定日志记录级别，Redis总共支持四个级别：debug、verbose、notice、warning，默认为verbose

logfile ""
# 日志文件配置,默认值为stdout，标准输出，若后台模式会输出到/dev/null

syslog-enabled
# 是否以syslog方式记录日志，yes开启no禁用，与该配置相关配置syslog-ident 和syslog-facility local0 分别是指明syslog的ident和facility

databases 16
#配置可用的数据库个数，默认值为16，默认数据库为0，数据库范围在0-（database-1）之间

always-show-logo yes #4.0以后新增配置
#是否配置日志显示redis徽标，yes显示no不显示


################################ 快照相关配置 #################################

save 900 1
save 300 10
save 60 10000
#配置快照(rdb)促发规则，格式：save <seconds> <changes>
#save 900 1  900秒内至少有1个key被改变则做一次快照
#save 300 10  300秒内至少有300个key被改变则做一次快照
#save 60 10000  60秒内至少有10000个key被改变则做一次快照

dbfilename  dump.rdb
#rdb持久化存储数据库文件名，默认为dump.rdb

stop-write-on-bgsave-error yes 
#yes代表当使用bgsave命令持久化出错时候停止写RDB快照文件，no则代表继续写

rdbchecksum yes
#开启rdb文件校验

dir "/etc"
#数据文件存放目录，rdb快照文件和aof文件都会存放至该目录


################################# 复制相关配置参数 #################################

slaveof <masterip> <masterport>  
#设置该数据库为其他数据库的从数据库，设置当本机为slave服务时，设置master服务的IP地址及端口，在Redis启动时，它会自动从master进行数据同步

masterauth <master-password>
#主从复制中，设置连接master服务器的密码（前提master启用了认证）

slave-serve-stale-data yes
# 当从库同主机失去连接或者复制正在进行，从机库有两种运行方式：
# 1) 如果slave-serve-stale-data设置为yes(默认设置)，从库会继续相应客户端的请求
# 2) 如果slave-serve-stale-data是指为no，除了INFO和SLAVOF命令之外的任何请求都会返回一个错误"SYNC with master in progress"

repl-ping-slave-period 10
#从库会按照一个时间间隔向主库发送PING命令来判断主服务器是否在线，默认是10秒

repl-timeout 60
#设置主库批量数据传输时间或者ping回复时间间隔超时时间，默认值是60秒
# 一定要确保repl-timeout大于repl-ping-slave-period

repl-backlog-size 1mb
#设置复制积压大小,只有当至少有一个从库连入才会释放。

slave-priority 100
#当主库发生宕机时候，哨兵会选择优先级最高的一个称为主库，从库优先级配置默认100，数值越小优先级越高

min-slaves-to-write 3
min-slaves-max-lag 10
#设置某个时间断内，如果从库数量小于该某个值则不允许主机进行写操作，以上参数表示10秒内如果主库的从节点小于3个，则主库不接受写请求，min-slaves-to-write 0代表关闭此功能。


################################## 安全相关配置 ###################################

requirepass
#客户端连接认证的密码，默认为空，即不需要密码，若配置则命令行使用AUTH进行认证

maxclients 10000
# 设置同一时间最大客户端连接数，4.0默认10000，Redis可以同时打开的客户端连接数为Redis进程可以打开的最大文件描述符数，
# 如果设置 maxclients 0，表示不作限制。
# 当客户端连接数到达限制时，Redis会关闭新的连接并向客户端返回max number of clients reached错误信息

maxmemory 4gb
#设置最大使用的内存大小

maxmemory-policy noeviction
#设置达到最大内存采取的策略：
# volatile-lru -> 利用LRU算法移除设置过过期时间的key (LRU:最近使用 Least Recently Used )
# allkeys-lru -> 利用LRU算法移除任何key
# volatile-random -> 移除设置过过期时间的随机key
# allkeys->random -> remove a random key, any key
# volatile-ttl -> 移除即将过期的key(minor TTL)
# 4.0默认noeviction代表不删除任何key，只在写操作时候返回错误。

maxmemory-samples 5
#LRU，LFU等算法样本设置，默认5个key


############################## AOF相关配置###############################

appendonly no
# 设置AOF持久化，yes开启，no禁用，开启后redis会把所接收到的每一次写操作请求都追加到appendonly.aof文件中，当redis重新启动时，会从该文件恢复出之前的状态。
# 但是这样会造成appendonly.aof文件过大，所以redis还支持了BGREWRITEAOF指令，对appendonly.aof 进行重写。

appendfilename "appendonly.aof"
#设置AOF文件名

appendfsync everysec
# AOF文件写策略，Redis支持三种同步AOF文件的策略:
# no: 不进行同步，交给操作系统去执行 ，速度较快
# always: always表示每次有写操作都调用fsync方法强制内核将该写操作写入到文件，速度会慢, 但是安全，因为每次写操作都在AOF文件中.
# everysec: 表示对写操作进行累积，每秒同步一次，折中方案.
# 默认是"everysec"，按照速度和安全折中这是最好的。

no-appendfsync-on-rewrite no
# AOF策略设置为always或者everysec时，后台处理进程(后台保存或者AOF日志重写)会执行大量的I/O操作
# 在某些Linux配置中会阻止过长的fsync()请求。注意现在没有任何修复，即使fsync在另外一个线程进行处理，为了减缓这个问题，可以设置下面这个参数no-appendfsync-on-rewrite

auto-aof-rewrite-percentage 100
auto-aof-rewrite-min-size 64mb
#当AOF文件增长到一定大小的时候Redis能够调用BGREWRITEAOF对日志文件进行重写，它是这样工作的：Redis会记住上次进行些日志后文件的大小(如果从开机以来还没进行过重写，那日子大小在开机的时候确定)。
#基础大小会同现在的大小进行比较。如果现在的大小比基础大小大制定的百分比，重写功能将启动
# 同时需要指定一个最小大小用于AOF重写，这个用于阻止即使文件很小但是增长幅度很大也去重写AOF文件的情况
# 设置 percentage 为0就关闭这个特性
#auto-aof-rewrite-percentage 代表AOF文件每次重写文件大小（以百分数代表），100表示百分之百，即当文件增加了1倍（100%），则开始重写AOF文件
#auto-aof-rewrite-min-size  设置最小重写文件大小，避免文件小而执行太多次的重写

aof-load-truncated yes
＃当redis突然运行崩溃时，会出现aof文件被截断的情况，Redis可以在发生这种情况时退出并加载错误，以下选项控制此行为。
＃如果aof-load-truncated设置为yes，则加载截断的AOF文件，Redis服务器启动发出日志以通知用户该事件。
＃否则，如果该选项设置为no，则服务器将中止并显示错误并停止启动。当该选项设置为no时，用户需要在重启之前使用“redis-check-aof”实用程序修复AOF文件在进行重启


################################## 慢查询配置 ###################################


slowlog-log-slower-than 10000
 #Redis Slow Log 记录超过特定执行时间的命令。执行时间不包括I/O计算比如连接客户端，返回结果等，只是命令执行时间，可以通过两个参数设置slow log：一个是告诉Redis执行超过多少时间被记录的参数slowlog-log-slower-than(微秒，因此1000000代表一分钟
#另一个是slow log 的长度。当一个新命令被记录的时候最早的命令将被从队列中移除
 
slowlog-max-len 128
#慢查询命令记录队列长度设置，该队列占用内存，可以使用SLOWLOG RESET清空队列



############################### 高级配置 ###############################

hash-max-zipmap-entries 512
hash-max-zipmap-value 64
# 当hash中包含超过指定元素个数并且最大的元素没有超过临界时，hash将以一种特殊的编码方式（大大减少内存使用）来存储，这里可以设置这两个临界值
# Redis Hash对应Value内部实际就是一个HashMap，实际这里会有2种不同实现，
# 这个Hash的成员比较少时Redis为了节省内存会采用类似一维数组的方式来紧凑存储，而不会采用真正的HashMap结构，对应的value redisObject的encoding为zipmap,当成员数量增大时会自动转成真正的HashMap,此时encoding为ht。

list-max-ziplist-size -2
#Lists也以特殊方式编码，以节省大量空间。
＃可以指定每个内部列表节点允许的条目数
＃作为固定的最大大小或最大元素数。
＃对于固定的最大大小，使用-5到-1表示：
＃-5：最大大小：64 Kb < - 不建议用于正常工作负载
＃-4：最大尺寸：32 Kb < - 不推荐
＃-3：最大尺寸：16 Kb < - 可能不推荐
＃-2：最大尺寸：8 Kb < - 好
＃-1：最大尺寸：4 Kb < - 良好
＃正数意味着存储_exactly_元素数量
＃每个列表节点。
＃性能最高的选项通常为-2（8 Kb大小）或-1（4 Kb大小）

zset-max-ziplist-entries 128
zset-max-ziplist-value 64
# list数据类型多少节点以下会采用去指针的紧凑存储格式。
# list数据类型节点值大小小于多少字节会采用紧凑存储格式。

activerehashing yes
# Redis将在每100毫秒时使用1毫秒的CPU时间来对redis的hash表进行重新hash，可以降低内存的使用
# 当你的使用场景中，有非常严格的实时性需要，不能够接受Redis时不时的对请求有2毫秒的延迟的话，把这项配置为no。
# 如果没有这么严格的实时性要求，可以设置为yes，以便能够尽可能快的释放内存

client-output-buffer-limit normal 0 0 0
client-output-buffer-limit slave 256mb 64mb 60
client-output-buffer-limit pubsub 32mb 8mb 60
#客户端输出缓冲区限制可用于强制断开客户端，由于某种原因，没有足够快地从服务器读取数据，常见的原因是Pub / Sub客户端不能像很快的消费一条消息，可以为三种不同类型的客户端设置不同的限制：
#normal - >普通客户端，包括MONITOR客户端
#subve - >从服务器客户端
#pubsub - >客户端订阅了至少一个pubsub通道或模式
#设置方法：client-output-buffer-limit 软限制大小 硬限制大小 秒数
#当客户端达到硬限制大小则立即断开连接，当客户端达到软限制时候并且在设置的秒数缓冲大小任然超了，则在设置的秒数后断开连接
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

## 四、数据类型以及相关操作

通常使用redis不外乎使用其常用的5中数据类型：string、list、hash、set、sorted_set,在3.2版本以后新添加geo经纬度支持，以下将对其类型的常用操作做说明。

### 命令使用前言

通大多数据库一样，redis所有的命令提供了帮助，可以使用help +命令名称查看其使用方法，帮助信息中不仅有命令用法，还有命令始于版本信息，分组等。

为了友好的使用，redis还将所有命令都进行了分组,同时使用help+@+组名进行查看每个组中所有命令，以下是所有分组信息。

上面以及介绍如何查看命令使用方法，所以在以下数据类型操作时候，只举例常用的命令，更多命令参考https://redis.io/commands

注意：redis在3.2版本新增geo数据类型。

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
generic       #一般命令组，对大多数类型适用
string        #字符串类型命令组，使用所有字符串类型
list          #列表类型命令组
set           #集合类型命令组
sorted_set    #有序集合命令组
hash          #hash操作命令组
pubsub        #发布命令组
transactions  #事务操作命令组
connection    #连接相关命令组
server        #服务器相关命令组
scripting     #lua 脚本命令组
hyperloglog   #hyperloglog类型命令组，redis在 2.8.9 版本添加了 HyperLogLog 结构
cluster       #集群相关命令组
geo           #经纬度相关命令组，适用于3.2.0以后的版本
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

示例：查看事务操作所有命令

![img](https://images2018.cnblogs.com/blog/1075473/201807/1075473-20180720163213695-601543803.png)

 

### key操作

常用：

```
DEL key #删除某个key
KEYS pattern  #查看符合正则的所有key
EXISTS key [key ...] #判断某个key是否存在，可支持多个，返回存在的个数
EXPIRE key seconds #刷新某个key过期时间
MOVE key db  #移动key到某个数据库
```

示例：

![img](https://images2018.cnblogs.com/blog/1075473/201807/1075473-20180723101026306-311240094.png)

### string操作

字符串操作中需要注意的是，redis中的整型也当作字符串处理。

常用：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
SET key value [EX seconds] [PX milliseconds] [NX|XX]  #设置key为指定的字符串值。
#参数：
#EX seconds – 设置键key的过期时间，单位时秒
#PX milliseconds – 设置键key的过期时间，单位时毫秒
#NX – 只有键key不存在的时候才会设置key的值
#XX – 只有键key存在的时候才会设置key的值

APPEND key value  #如果 key 已经存在，并且值为字符串，那么这个命令会把 value 追加到原来值（value）的结尾。 如果 key 不存在，那么它将首先创建一个空字符串的key，再执行追加操作，这种情况 APPEND 将类似于 SET 操作。

GET key #获取key值，不存在则返回nil

GETRANGE key start end #获取指定key值的索引开始位置和结束位置所对应的值，索引从0开始

GETSET key value  #设置新的key值，并获取设置之前的值，如果key不存在则设置，并返回nil

MGET key [key ...]   #批量获取key的值

MSET key value [key value ...] #批量设置key的值

DECR key #数字类型的key自减操作，key类型不是数字则报错

INCR key  #数字类型key 自加操作，与DECR相反

DECRBY key decrement  #数字类型key指定减少数值

INCRBY key increment   #数字类型key指定增加数值，与DECRBY相反

STRLEN key  #获取key长度
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

示例：

![img](https://images2018.cnblogs.com/blog/1075473/201807/1075473-20180723111202450-413865489.png)

### list操作

列表中的元素索引从0开始，倒数的元素可以用“-”+倒数位置表示，如-2，代表倒数第二个元素，-1则代表最后一个元素。

Redis列表是简单的字符串列表，按照插入顺序排序。你可以添加一个元素到列表的头部（左边）或者尾部（右边。

一个列表最多可以包含 232 - 1 个元素 (4294967295, 每个列表超过40亿个元素)。

常用：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
LPUSH key value [value ...]  #从列表左边放入一个或者多个元素

LPUSHX key value  #当列表存在时，从左边放入一个元素

RPUSH key value [value ...]  #从列表右边放入一个或者多个元素

RPUSHX key value  #当列表存在时，从右边放入一个元素

LSET key index value  #根据索引设置列表中元素的值,当list不存在是报错

LINDEX key index  #根据列表索引获取元素值，索引从0开始

LINSERT key BEFORE|AFTER pivot value  #在列表中，基于某个基准点插入值，pivot代表基准点

LLEN key #获取列表长度

LRANGE key start stop  #根据索引获取列表中的元素，列表索引最后一个可以使用-1

LREM key count value  #从存于 key 的列表里移除前 count 次出现的值为 value 的元素
#count > 0: 从头往尾移除值为 value 的元素
#count < 0: 从尾往头移除值为 value 的元素
#count = 0: 移除所有值为 value 的元素

LPOP key  #从列表左边删除一个元素

RPOP key  #从列表右边删除一个元素

RPOPLPUSH source destination  #删除source列表中的删除最后一个元素将其追加到destination列表

LTRIM key start stop  #根据索引start和stop保留列表元素
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

示例：

![img](https://images2018.cnblogs.com/blog/1075473/201807/1075473-20180723143235852-2005355974.png)

### hash操作

hash操作所有命令都以H开头。

Redis hash 是一个string类型的field和value的映射表，hash特别适合用于存储对象。

Redis 中每个 hash 可以存储 232 - 1 键值对（40多亿）。

常用：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
HDEL key field [field ...]  #删除hash表中一个或多个字段

HEXISTS key field  #判断hash表中字段是否存在

HGET key field  #获取hash表中字段的值

HGETALL key  #获取hash表中所有字段

HSET key field value  # 设置hash表中字段的值

HSETNX key field value  #只有当字段不存在时候才设置hash表中字段值，

HLEN key  #获取hash表中字段个数

HVALS key  #获取hash表中所有字段的值

HKEYS key  #获取hash表中所有的字段

HSTRLEN key field #获取hash表中指定字段的值的长度

HMSET key field value [field value ...]  #批量设置hash表中字段的值

HMGET key field [field ...]  #批量获取hash表中字段的值
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

示例：

![img](https://images2018.cnblogs.com/blog/1075473/201807/1075473-20180723150640062-1294716815.png)

### 集合set操作

Redis 的 Set 是 String 类型的无序集合。集合成员是唯一的，这就意味着集合中不能出现重复的数据。

Redis 中集合是通过哈希表实现的，所以添加，删除，查找的复杂度都是 O(1)。

集合中最大的成员数为 232 - 1 (4294967295, 每个集合可存储40多亿个成员)。

常用：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
SADD key member [member ...]  #添加一个或多个元素到集合中

SREM key member [member ...]  #删除一个或多个集合中的元素

SCARD key  #获取集合中元素数量

SMEMBERS key  #返回集合中所有的元素

SINTER key [key ...] #获取两个或两个以上集合的交集

SUNION key [key ...]  #获取两个或两个以上集合的并集

SDIFF key [key ...]     #获取两个或者两个以上集合的差集

SISMEMBER key member  #判断元素是否是在指定集合中

SMOVE source destination member #移动一个集合中的元素到另一个集合

SPOP key [count]  #移除count个集合中元素，count可选参数，默认为1，即移除一个
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

### 有序集合操作

Redis 有序集合和集合一样也是string类型元素的集合,且不允许重复的成员。

不同的是每个元素都会关联一个double类型的分数。redis正是通过分数来为集合中的成员进行从小到大的排序。

有序集合的成员是唯一的,但分数(score)却可以重复。

集合是通过哈希表实现的，所以添加，删除，查找的复杂度都是O(1)。 集合中最大的成员数为 232 - 1 (4294967295, 每个集合可存储40多亿个成员)。

常用：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
ZADD key [NX|XX] [CH] [INCR] score member [score member ...]  #向一个有序集合添加成员（元素）
#参数：
#XX: 仅仅更新存在的成员，不添加新成员。
#NX: 不更新存在的成员。只添加新成员。
#CH: 修改返回值为发生变化的成员总数，原始是返回新添加成员的总数 (CH 是 changed 的意思)。更改的元素是新添加的成员，已经存在的成员更新分数。 所以在命令中指定的成员有相同的分数将不被计算在内。注：在通常情况下，ZADD返回值只计算新添加成员的数量。
#INCR: 当ZADD指定这个选项时，成员的操作就等同ZINCRBY命令，对成员的分数进行递增操作。

ZCARD key  #获取有序集合中元素个数

ZCOUNT key min max  #指定分数范围的元素个数

ZINCRBY key increment member  #为有序集的元素的score值加上增加指定的increment

ZRANGE key start stop [WITHSCORES]  #根据有序集合中分数区间获取集合中的元素

ZRANGE key start stop [WITHSCORES]  #获取有序集合中元素的排名

ZREM key member [member ...]  #删除有序集合中一个或多个元素

ZSCORE key member  #设置元素在集合中的分数
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

### GEO类型操作

Redis的GEO是 3.2 版本的新特性，对GEO(地理位置)的支持。这个功能可以将用户给定的地理位置信息储存起来， 并对这些信息进行操作。

geo类型命令不多，总共6个所以这里全部列举出来了。

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
GEOADD key longitude latitude member [longitude latitude member ...]  #将指定的地理空间位置（纬度、经度、名称）添加到指定的key中

GEODIST key member1 member2 [unit]  #返回两个给定位置之间的距离。如果两个位置之间的其中一个不存在， 那么命令返回空值。指定单位的参数 unit 必须是以下单位的其中一个：

#m 表示单位为米
#km 表示单位为千米
#mi 表示单位为英里
#ft 表示单位为英尺

GEOPOS key member [member ...]  #从key里返回所有给定位置元素的位置（经度和纬度）

GEOHASH key member [member ...]  #返回一个或多个位置元素的 Geohash 表示。通常使用表示位置的元素使用不同的技术，使用Geohash位置52点整数编码。由于编码和解码过程中所使用的初始最小和最大坐标不同，编码的编码也不同于标准。此命令返回一个标准的Geohash

GEORADIUS key longitude latitude radius m|km|ft|mi [WITHCOORD] [WITHDIST] [WITHHASH] [COUNT count] [ASC|DESC] [STORE key] [STOREDIST key]  
#以给定的经纬度为中心， 返回键包含的位置元素当中， 与中心的距离不超过给定最大距离的所有位置元素。

#范围可以使用以下其中一个单位：

#m 表示单位为米。
#km 表示单位为千米。
#mi 表示单位为英里。
#ft 表示单位为英尺。
#在给定以下可选项时， 命令会返回额外的信息：

#WITHDIST: 在返回位置元素的同时， 将位置元素与中心之间的距离也一并返回。 距离的单位和用户给定的范围单位保持一致。
#WITHCOORD: 将位置元素的经度和维度也一并返回。
#WITHHASH: 以 52 位有符号整数的形式， 返回位置元素经过原始 geohash 编码的有序集合分值。 这个选项主要用于底层应用或者调试， 实际中的作用并不大。
#命令默认返回未排序的位置元素。 通过以下两个参数， 用户可以指定被返回位置元素的排序方式：

#ASC: 根据中心的位置， 按照从近到远的方式返回位置元素。
#DESC: 根据中心的位置， 按照从远到近的方式返回位置元素。
#在默认情况下， GEORADIUS 命令会返回所有匹配的位置元素。 虽然用户可以使用 COUNT <count> 选项去获取前 N 个匹配元素， 但是因为命令在内部可能会需要对所有被匹配的元素进行处理， 所以在对一个非常大的区域进行搜索时， 即使只使用 COUNT 选项去获取少量元素， 命令的执行速度也可能会非常慢。 但是从另一方面来说， 使用 COUNT 选项去减少需要返回的元素数量， 对于减少带宽来说仍然是非常有用的。

#返回值：
  #在没有给定任何 WITH 选项的情况下， 命令只会返回一个像 [“New York”,”Milan”,”Paris”] 这样的线性（linear）列表。
  #在指定了 WITHCOORD 、 WITHDIST 、 WITHHASH 等选项的情况下， 命令返回一个二层嵌套数组， 内层的每个子数组就表示一个元素

  #在返回嵌套数组时， 子数组的第一个元素总是位置元素的名字。 至于额外的信息， 则会作为子数组的后续元素， 按照以下顺序被返回：
    #以浮点数格式返回的中心与位置元素之间的距离， 单位与用户指定范围时的单位一致。
    #geohash 整数。
    #由两个元素组成的坐标，分别为经度和纬度。

GEORADIUSBYMEMBER key member radius m|km|ft|mi [WITHCOORD] [WITHDIST] [WITHHASH] [COUNT count] [ASC|DESC] [STORE key] [STOREDIST key]
#这个命令和 GEORADIUS 命令一样， 都可以找出位于指定范围内的元素， 但是 GEORADIUSBYMEMBER 的中心点是由给定的位置元素决定的。
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

操作示例：

![img](https://images2018.cnblogs.com/blog/1075473/201807/1075473-20180723173956118-272001640.png)

## 五、发布订阅

Redis 发布订阅(pub/sub)是一种消息通信模式：发送者(pub)发送消息，订阅者(sub)接收消息。

Redis 客户端可以订阅任意数量的频道。

下图代表其发布订阅之间的关系

![img](https://images2018.cnblogs.com/blog/1075473/201807/1075473-20180724102046761-1929696308.png)

 

### 运作原理

每个Redis 服务器进程都维持着一个表示服务器状态的 redis.h/redisServer结构， 结构的pubsub_channels 属性是一个字典， 这个字典就用于保存订阅频道的信息：

```
struct redisServer {
    // ...
    dict *pubsub_channels;
    // ...
};
```

其中，字典的键为正在被订阅的频道， 而字典的值则是一个链表， 链表中保存了所有订阅这个频道的客户端。
比如说，在下图展示的这个pubsub_channels示例中，client1 、 client2 和 client3 就订阅了 channel1 ， 而client3也同时订阅了channel2。
当客户端调用SUBSCRIBE命令时， 程序就将客户端和要订阅的频道在pubsub_channels字典中关联起来。

![img](https://images2018.cnblogs.com/blog/1075473/201807/1075473-20180724105321134-1954840099.png)

 

SUBSCRIBE 命令的行为可以用伪代码表示如下：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
def SUBSCRIBE(client, channels):

    // 遍历所有输入频道
    for channel in channels:

        // 将客户端添加到链表的末尾
        redisServer.pubsub_channels[channel].append(client)
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

通过pubsub_channels字典， 程序只要检查某个频道是否为字典的键， 就可以知道该频道是否正在被客户端订阅； 只要取出某个键的值， 就可以得到所有订阅该频道的客户端的信息。

了解了pubsub_channels字典的结构之后， 解释PUBLISH命令的实现就非常简单了： 当调用PUBLISH channel message命令， 程序首先根据channel定位到字典的键， 然后将信息发送给字典值链表中的所有客户端。

### 订阅模式

redis的发布订阅不仅仅提供简单的订阅频道，还提供模式匹配订阅。模式订阅使用命令PSUBSCRIBE实现。

redisServer.pubsub_patterns属性是一个链表，链表中保存着所有和模式相关的信息：

```
struct redisServer {
    // ...
    list *pubsub_patterns;
    // ...
};
```

链表中的每个节点都包含一个redis.h/pubsubPattern结构：

```
typedef struct pubsubPattern {
    redisClient *client;
    robj *pattern;
} pubsubPattern;
```


client 属性保存着订阅模式的客户端，而 pattern 属性则保存着被订阅的模式。
每当调用 PSUBSCRIBE命令订阅一个模式时， 程序就创建一个包含客户端信息和被订阅模式的pubsubPattern结构， 并将该结构添加到redisServer.pubsub_patterns链表中。
作为例子，下图展示了一个包含两个模式的 pubsub_patterns 链表， 其中 client123 和 client256 都正在订阅 tweet.shop.* 模式：

![img](https://images2018.cnblogs.com/blog/1075473/201807/1075473-20180724112542370-1337959980.png)

通过遍历整个pubsub_patterns链表，程序可以检查所有正在被订阅的模式，以及订阅这些模式的客户端。

当执行PUBLISH进行命令向channel命令发送消息时，PUBLISH除了将message 发送到所有订阅channel的客户端之外， 它还会将channel和pubsub_patterns中的模式进行对比， 如果channel和某个模式匹配的话， 那么也将message 发送到订阅那个模式的客户端，例如一个客户端订阅了aa.bb.*频道，那么他会收到来自所有aa.bb开头的所有频道消息。

### 相关命令

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
PSUBSCRIBE pattern [pattern ...]  #使用模式订阅一个或多个符合给定模式的频道

PUNSUBSCRIBE [pattern [pattern ...]]  #退订所有给定模式的频道

SUBSCRIBE channel [channel ...]   #订阅给定的一个或多个频道的信息

UNSUBSCRIBE [channel [channel ...]]   #指退订给定的频道

PUBSUB subcommand [argument [argument ...]]  #查看订阅与发布系统状态

PUBLISH channel message   #将信息发送到指定的频道
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

### 实践

在以下示例中，将分别用SUBSCRIBE命令订阅aa.bb和使用PSUBSCRIBE模式订阅频道aa.bb*。

SUBSCRIBE订阅：

![img](https://images2018.cnblogs.com/blog/1075473/201807/1075473-20180724113421089-2081098527.png)

PSUBSCRIBE订阅：

![img](https://images2018.cnblogs.com/blog/1075473/201807/1075473-20180724113550043-1493570562.png)

此时我们使用PUBSH向aa.bb发送消息，返回接受到的频道数，两个订阅者都能收到消息。

![img](https://images2018.cnblogs.com/blog/1075473/201807/1075473-20180724113748805-271826950.png)

订阅者1:

![img](https://images2018.cnblogs.com/blog/1075473/201807/1075473-20180724113858218-1536831670.png)

模式订阅者：

![img](https://images2018.cnblogs.com/blog/1075473/201807/1075473-20180724114007352-1131458355.png)

### 小结

- 订阅信息由服务器进程维持的redisServer.pubsub_channels字典保存，字典的键为被订阅的频道，字典的值为订阅频道的所有客户端。
- 当有新消息发送到频道时，程序遍历频道（键）所对应的（值）所有客户端，然后将消息发送到所有订阅频道的客户端上。
- 订阅模式的信息由服务器进程维持的redisServer.pubsub_patterns链表保存，链表的每个节点都保存着一个pubsubPattern结构，结构中保存着被订阅的模式，以及订阅该模式的客户端。程序通过遍历链表来查找某个频道是否和某个模式匹配。
- 当有新消息发送到频道时，除了订阅频道的客户端会收到消息之外，所有订阅了匹配频道的模式的客户端，也同样会收到消息。
- 退订频道和退订模式分别是订阅频道和订阅模式的反操作。

## 六、事务

所谓事务应具有以下特效：原子性(Atomicity)， 一致性(Consistency)，隔离性(Isolation)，持久性(Durability)，简称ACID，但redis所提供的事务比较简单，它通过MULTI、EXEC、DISCARD和WATCH等命令实现事务。

而Redis只支持简单的事务，将执行命令放入队列缓存，当程序中有异常或命令出错，执行DISCARD清空缓存队列不执行队列中命令，其事务过程有以下特点：

- 事务是一个单独的隔离操作：事务中的所有命令都会序列化、按顺序地执行。事务在执行的过程中，不会被其他客户端发送来的命令请求所打断。
- 事务是一个**泛原子**操作（这里我以泛原子称呼，在某些情况redis的事务不是原子性的，后续会说明）：事务中的命令要么全部被执行，要么全部都不执行。

EXEC 命令负责触发并执行事务中的所有命令：

- 如果客户端在使用 MULTI 开启了一个事务之后，却因为断线而没有成功执行 EXEC ，那么事务中的所有命令都不会被执行。
- 另一方面，如果客户端成功在开启事务之后执行 EXEC ，那么事务中的所有命令都会被执行。

特别说明文中的**泛原子操作**：

- redis在开启事务以后，若执行命令具有显示的错误或者客户端中断则此次事务在执行EXEC命令时会调用DISCARD清空缓存队列不执行队列中的所有任务，此时是原子性的。
- 当执行命令过程中，命令没有显示的报错（例如LSET操作设置一个不存在的list），而是在EXEC调用时候某个命令出错，那么在这之前已经执行的命令将不会回滚，所以严格说来，redis并不支持原子性。

### 涉及命令

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
MULTI  #用于标记事务块的开始。Redis会将后续的命令逐个放入队列中，然后才能使用EXEC命令执行缓存队列中的命令。

EXEC  #执行缓存队列中的命令

DISCARD  #清除所有先前在一个事务中放入队列的命令，然后恢复正常的连接状态，如果使用了WATCH命令，那么DISCARD命令就会将当前连接监控的所有键取消监控。

WATCH key [key ...]   #当某个事务需要按条件执行时，就要使用这个命令将给定的键设置为受监控的

UNWATCH  #清除所有先前为一个事务监控的键，如果你调用了EXEC或DISCARD命令，那么就不需要手动调用UNWATCH命令
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

### 乐观锁机制

乐观锁：总是认为不会产生并发问题，每次去取数据的时候总认为不会有其他线程对数据进行修改，因此不会上锁，但是在更新时会判断其他线程在这之前有没有对数据进行修改，一般会使用版本号机制或检查再设置(CAS)操作实现。

redis通过WATCH命令实现乐观锁，作为WATCH命令的参数的键会受到Redis的监控，Redis能够检测到它们的变化。在执行EXEC命令之前，如果Redis检测到至少有一个键被修改了，那么整个事务便会中止运行，然后EXEC命令会返回一个nil值，提醒用户事务运行失败。

注意：WATCH命令需要在MULTI之前执行，不然redis会将其一个命令放入缓存队列中。

示例：在以下示例中通过一个客户端开启事务监听name键，另一个客户端在执行EXEC之前修改name键，此次事务将不会执行，并返回nil，如下。

![img](https://images2018.cnblogs.com/blog/1075473/201807/1075473-20180724150037057-928572007.png)

![img](https://images2018.cnblogs.com/blog/1075473/201807/1075473-20180724150504414-1451703303.png)

 

### 原子性实践

为演示redis严格意义上将不支持原子性，做了一些简单实践。

![img](https://images2018.cnblogs.com/blog/1075473/201807/1075473-20180724152337730-2016437575.png)

从上面的结果可以看出，在开启事务前name 值为Rose，在开启事务先后执行了SET命令和LSET命令，但是LSET命令是错误的，当我们调用EXEC执行事务完事务以后，在回头看事务中的SET命令已经生效，并未回滚，因为在次过程中该命令没有显示的报错，所以可以说redis的事务不支持原子性。

## 一、RDB持久化

### 简介

RDB持久化方式是通过快照(snapshotting)完成的，当符合一定条件时，redis会自动将内存中所有数据以二进制方式生成一份副本并存储在硬盘上。当redis重启时，并且AOF持久化未开启时，redis会读取RDB持久化生成的二进制文件(默认名称dump.rdb，可通过设置dbfilename修改)进行数据恢复，对于持久化信息可以用过命令“info Persistence”查看。

### 快照文件位置

RDB快照文件存储文件位置由dir配置参数指明，文件名由dbfilename指定，如下：

![img](https://images2018.cnblogs.com/blog/1075473/201807/1075473-20180725192452019-1361880741.png)

 

### 快照触发条件

RDB生成快照可自动促发，也可以使用命令手动触发，以下是redis触发执行快照条件，后续会对每个条件详细说明：

1. 客户端执行命令save和bgsave会生成快照；
2. 根据配置文件save m n规则进行自动快照；
3. 主从复制时，从库全量复制同步主库数据，此时主库会执行bgsave命令进行快照；
4. 客户端执行数据库清空命令FLUSHALL时候，触发快照；
5. 客户端执行shutdown关闭redis时，触发快照；

 

### save命令触发

客户端执行save命令，该命令强制redis执行快照，这时候redis处于阻塞状态，不会响应任何其他客户端发来的请求，直到RDB快照文件执行完毕，所以请慎用。

实践操作：

首先使用info Persistence查看最近一次持久化时间：

![img](https://images2018.cnblogs.com/blog/1075473/201807/1075473-20180725162051140-115934526.png)

此时我们执行save命令，并再次查看最新快照保存时间已经是最新一次时间：

![img](https://images2018.cnblogs.com/blog/1075473/201807/1075473-20180725162342372-831597131.png)

当然你也可以直接查看RDB数据文件目录下的RDB文件最新时间：

![img](https://images2018.cnblogs.com/blog/1075473/201807/1075473-20180725163048654-1985991165.png)

 

### bgsave命令触发

bgsave命令可以理解为background save即：“后台保存”。当执行bgsave命令时，redis会fork出一个子进程来执行快照生成操作，需要注意的redis是在fork子进程这个简短的时间redis是阻塞的（此段时间不会响应客户端请求，），当子进程创建完成以后redis响应客户端请求。其实redis自动快照也是使用bgsave来完成的。

为了能清楚了解bgsave工作过程，以下将图文详细描述其工作过程：

![img](https://images2018.cnblogs.com/blog/1075473/201807/1075473-20180725172641202-1573986143.png)

对上述过程描述：

1. 客户端执行bgsave命令，redis主进程收到指令并判断此时是否在执行bgrewriteaof(AOF文件重新过程，后续会讲解)，如果此时正好在执行则bgsave直接返回，不fork子进程，如果没有执行bgrewriteaof重写AOF文件，则进入下一个阶段；
2. 主进程调用fork方法创建子进程，在创建过程中redis主进程阻塞，所以不能响应客户端请求；
3. 子进程创建完成以后，bgsave命令返回“Background saving started”，此时标志着redis可以响应客户端请求了；
4. 子经常根据主进程的内存副本创建临时快照文件，当快照文件完成以后对原快照文件进行替换；
5. 子进程发送信号给redis主进程完成快照操作，主进程更新统计信息（info Persistence可查看）,子进程退出；

 

实践操作：

执行bgsave

![img](https://images2018.cnblogs.com/blog/1075473/201807/1075473-20180725180545944-1115113428.png)

查看日志，能看到6MB文件内存副本写到了磁盘上，同时打印“Background saving terminated with success”代表文件bgsave操作完成。

![img](https://images2018.cnblogs.com/blog/1075473/201807/1075473-20180725180641008-453656258.png)

此时我们查看统计信息最后一次RDB保存时间已经更新：

![img](https://images2018.cnblogs.com/blog/1075473/201807/1075473-20180725181147875-1551556683.png)

 

###  save m n规则触发 

save m n规则说明：在指定的m秒内，redis中有n个键发生改变，则自动触发bgsave。该规则默认也在redis.conf中进行了配置，并且可组合使用，满足其中一个规则，则触发bgsave，在上篇博文也进行了解释，如下：

![img](https://images2018.cnblogs.com/blog/1075473/201807/1075473-20180725182526630-131017848.png)

以save 900 1为例，表明当900秒内至少有一个键发生改变时候，redis触发bgsave操作。

 

实践操作：

我们改变一个键，满足save 900 1 ：

![img](https://images2018.cnblogs.com/blog/1075473/201807/1075473-20180725183245166-679592639.png)

此时查看redis日志，会发现redis立即响应开始bgsave操作：

![img](https://images2018.cnblogs.com/blog/1075473/201807/1075473-20180725183320854-1194188372.png)

 

### FLUSHALL触发

flushall命令用于清空数据库，请慎用，当我们使用了则表明我们需要对数据进行清空，那redis当然需要对快照文件也进行清空，所以会触发bgsave。

实践操作：

![img](https://images2018.cnblogs.com/blog/1075473/201807/1075473-20180725185027573-2116464119.png)

日志：

![img](https://images2018.cnblogs.com/blog/1075473/201807/1075473-20180725185106757-306359657.png)

 

### shutdown触发

shutdown命令触发就不用说了,redis在关闭前处于安全角度将所有数据全部保存下来，以便下次启动会恢复。

实践操作：

可以使用客户端连入执行shutdown命令，也可以直接使用脚本关闭redis,这里我使用init脚本（系统centos6.X）。

![img](https://images2018.cnblogs.com/blog/1075473/201807/1075473-20180725185451934-1052283048.png)

查看日志：

![img](https://images2018.cnblogs.com/blog/1075473/201807/1075473-20180725185627452-1839816217.png)

 

### 主从触发

在redis主从复制中，从节点执行全量复制操作，主节点会执行bgsave命令，并将rdb文件发送给从节点，该过程会在复制篇中进行阐述。

 

### 故障恢复

上面提及到过，当redis意外崩溃或者关闭再次启动时，此时AOF持久化未开启时(默认未开启)，将使用RDB快照文件恢复数据。

下面我们停用redis服务来模拟故障情况，让再启动redis服务：

![img](https://images2018.cnblogs.com/blog/1075473/201807/1075473-20180726091716171-1101078702.png)

观察日志会发现，启动时候load RDB文件。

![img](https://images2018.cnblogs.com/blog/1075473/201807/1075473-20180726091825425-686359421.png)

 

### RDB持久化配置

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
save m n
#配置快照(rdb)促发规则，格式：save <seconds> <changes>
#save 900 1  900秒内至少有1个key被改变则做一次快照
#save 300 10  300秒内至少有300个key被改变则做一次快照
#save 60 10000  60秒内至少有10000个key被改变则做一次快照
#关闭该规则使用svae “” 

dbfilename  dump.rdb
#rdb持久化存储数据库文件名，默认为dump.rdb

stop-write-on-bgsave-error yes 
#yes代表当使用bgsave命令持久化出错时候停止写RDB快照文件,no表明忽略错误继续写文件。

rdbchecksum yes
#在写入文件和读取文件时是否开启rdb文件检查，检查是否有无损坏，如果在启动是检查发现损坏，则停止启动。

dir "/etc/redis"
#数据文件存放目录，rdb快照文件和aof文件都会存放至该目录，请确保有写权限

rdbcompression yes
#是否开启RDB文件压缩，该功能可以节约磁盘空间
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

 

##  二、AOF持久化

### 简介

当redis存储非临时数据时，为了降低redis故障而引起的数据丢失，redis提供了AOF(Append Only File)持久化，从单词意思讲，将命令追加到文件。AOF可以将Redis执行的每一条写命令追加到磁盘文件(appendonly.aof)中,在redis启动时候优先选择从AOF文件恢复数据。由于每一次的写操作，redis都会记录到文件中，所以开启AOF持久化会对性能有一定的影响，但是大部分情况下这个影响是可以接受的，我们可以使用读写速率高的硬盘提高AOF性能。与RDB持久化相比，AOF持久化数据丢失更少，其消耗内存更少(RDB方式执行bgsve会有内存拷贝)。

 

### 开启AOF

默认情况下，redis是关闭了AOF持久化，开启AOF通过配置appendonly为yes开启，我们修改配置文件或者在命令行直接使用config set修改，在用config rewrite同步到配置文件。通过客户端修改好处是不用重启redis，AOF持久化直接生效。

![img](https://images2018.cnblogs.com/blog/1075473/201807/1075473-20180726104425988-577848390.png)

 

### AOF持久化过程 

redisAOF持久化过程可分为以下阶段：

1.追加写入

　　redis将每一条写命令以redis通讯协议添加至缓冲区aof_buf,这样的好处在于在大量写请求情况下，采用缓冲区暂存一部分命令随后根据策略一次性写入磁盘，这样可以减少磁盘的I/O次数，提高性能。

2.同步命令到硬盘

　　当写命令写入aof_buf缓冲区后，redis会将缓冲区的命令写入到文件，redis提供了三种同步策略，由配置参数appendfsync决定，下面是每个策略所对应的含义：

- no：不使用fsync方法同步，而是交给操作系统write函数去执行同步操作，在linux操作系统中大约每30秒刷一次缓冲。这种情况下，缓冲区数据同步不可控，并且在大量的写操作下，aof_buf缓冲区会堆积会越来越严重，一旦redis出现故障，数据丢失严重。
- always：表示每次有写操作都调用fsync方法强制内核将数据写入到aof文件。这种情况下由于每次写命令都写到了文件中, 虽然数据比较安全，但是因为每次写操作都会同步到AOF文件中，所以在性能上会有影响，同时由于频繁的IO操作，硬盘的使用寿命会降低。
- everysec：数据将使用调用操作系统write写入文件，并使用fsync每秒一次从内核刷新到磁盘。 这是折中的方案，兼顾性能和数据安全，所以redis默认推荐使用该配置。

3.文件重写(bgrewriteaof)

　　当开启的AOF时，随着时间推移，AOF文件会越来越大,当然redis也对AOF文件进行了优化，即触发AOF文件重写条件（后续会说明）时候，redis将使用bgrewriteaof对AOF文件进行重写。这样的好处在于减少AOF文件大小，同时有利于数据的恢复。

　　为什么重写？比如先后执行了“set foo bar1 set foo bar2 set foo bar3” 此时AOF文件会记录三条命令，这显然不合理，因为文件中应只保留“set foo bar3”这个最后设置的值，前面的set命令都是多余的，下面是一些重写时候策略：

- 重复或无效的命令不写入文件
- 过期的数据不再写入文件
- 多条命令合并写入（当多个命令能合并一条命令时候会对其优化合并作为一个命令写入，例如“RPUSH list1 a RPUSH list1 b" 合并为“RPUSH list1 a b” ）

　　

### 重写触发条件 

AOF文件触发条件可分为手动触发和自动触发：

手动触发：客户端执行bgrewriteaof命令。

自动触发：自动触发通过以下两个配置协作生效：

- auto-aof-rewrite-min-size: AOF文件最小重写大小，只有当AOF文件大小大于该值时候才可能重写,4.0默认配置64mb。
- auto-aof-rewrite-percentage：当前AOF文件大小和最后一次重写后的大小之间的比率等于或者等于指定的增长百分比，如100代表当前AOF文件是上次重写的两倍时候才重写。　

redis开启在AOF功能开启的情况下，会维持以下三个变量

- 记录当前AOF文件大小的变量aof_current_size。
- 记录最后一次AOF重写之后，AOF文件大小的变量aof_rewrite_base_size。
- 增长百分比变量aof_rewrite_perc。

每次当serverCron（服务器周期性操作函数）函数执行时，它会检查以下条件是否全部满足，如果全部满足的话，就触发自动的AOF重写操作：

- 没有BGSAVE命令（RDB持久化）/AOF持久化在执行；
- 没有BGREWRITEAOF在进行；
- 当前AOF文件大小要大于server.aof_rewrite_min_size的值；
- 当前AOF文件大小和最后一次重写后的大小之间的比率等于或者大于指定的增长百分比（auto-aof-rewrite-percentage参数）

### 重写过程

　　AOF文件重写过程与RDB快照bgsave工作过程有点相似，都是通过fork子进程，由子进程完成相应的操作，同样的在fork子进程简短的时间内，redis是阻塞的，以下图文说明其重写过程：

![img](https://images2018.cnblogs.com/blog/1075473/201807/1075473-20180726171841786-525684493.png)

 

过程说明：

　　aof_rewrite_buf 代表重写缓冲区      aof_buf代表写写命令存放的缓冲区

　　1.开始bgrewriteaof，判断当前有没有bgsave命令(RDB持久化)/bgrewriteaof在执行，倘若有，则这些命令执行完成以后在执行。

　　2.主进程fork出子进程，在这一个短暂的时间内，redis是阻塞的。

　　3.主进程fork完子进程继续接受客户端请求，所有写命令依然写入AOF文件缓冲区并根据appendfsync策略同步到磁盘，保证原有AOF文件完整和正确。由于fork的子进程仅仅只共享主进程fork时的内存，因此Redis使用采用重写缓冲区(aof_rewrite_buf)机制保存fork之后的客户端的写请求，防止新AOF文件生成期间丢失这部分数据。此时，客户端的写请求不仅仅写入原来aof_buf缓冲，还写入重写缓冲区(aof_rewrite_buf)。

　　4.子进程通过内存快照，按照命令重写策略写入到新的AOF文件。

　　4.1子进程写完新的AOF文件后，向主进程发信号，父进程更新统计信息。

　　4.2主进程把AOFaof_rewrite_buf中的数据写入到新的AOF文件(避免写文件是数据丢失)。

　　5.使用新的AOF文件覆盖旧的AOF文件，标志AOF重写完成。

 

### AOF实现本质

AOF实现本质是基于redis通讯协议，将命令以纯文本的方式写入到文件中。

redis协议：

首先Redis是以行来划分，每行以\r\n行结束。每一行都有一个消息头，消息头共分为5种分别如下:

(+) 表示一个正确的状态信息，具体信息是当前行+后面的字符。

(-)  表示一个错误信息，具体信息是当前行－后面的字符。

(*) 表示消息体总共有多少行，不包括当前行,*后面是具体的行数。

($) 表示下一行数据长度，不包括换行符长度\r\n,$后面则是对应的长度的数据。

(:) 表示返回一个数值，：后面是相应的数字节符。

我们可以直接查看AOF文件中的格式，如下图：

![img](https://images2018.cnblogs.com/blog/1075473/201807/1075473-20180726164713917-221284785.png)

 

### 数据恢复

之前已经提到当AOF开启时候，redis数据恢复优先选用AOF进行数据恢复，以下使用停止redis来模拟redis故障,然后在重写启动进行恢复。

![img](https://images2018.cnblogs.com/blog/1075473/201807/1075473-20180726165343603-2065130718.png)

查看日志会发现数据恢复已经变成从AOF(append only file)文件中恢复：![img](https://images2018.cnblogs.com/blog/1075473/201807/1075473-20180726165528177-1455389442.png)

 

### AOF配置参数

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
auto-aof-rewrite-min-size 64mb
#AOF文件最小重写大小，只有当AOF文件大小大于该值时候才可能重写,4.0默认配置64mb。

auto-aof-rewrite-percentage  100
#当前AOF文件大小和最后一次重写后的大小之间的比率等于或者等于指定的增长百分比，如100代表当前AOF文件是上次重写的两倍时候才重写。

appendfsync everysec
#no：不使用fsync方法同步，而是交给操作系统write函数去执行同步操作，在linux操作系统中大约每30秒刷一次缓冲。这种情况下，缓冲区数据同步不可控，并且在大量的写操作下，aof_buf缓冲区会堆积会越来越严重，一旦redis出现故障，数据
#always：表示每次有写操作都调用fsync方法强制内核将数据写入到aof文件。这种情况下由于每次写命令都写到了文件中, 虽然数据比较安全，但是因为每次写操作都会同步到AOF文件中，所以在性能上会有影响，同时由于频繁的IO操作，硬盘的使用寿命会降低。
#everysec：数据将使用调用操作系统write写入文件，并使用fsync每秒一次从内核刷新到磁盘。 这是折中的方案，兼顾性能和数据安全，所以redis默认推荐使用该配置。

aof-load-truncated yes
#当redis突然运行崩溃时，会出现aof文件被截断的情况，Redis可以在发生这种情况时退出并加载错误，以下选项控制此行为。
#如果aof-load-truncated设置为yes，则加载截断的AOF文件，Redis服务器启动发出日志以通知用户该事件。
#如果该选项设置为no，则服务将中止并显示错误并停止启动。当该选项设置为no时，用户需要在重启之前使用“redis-check-aof”实用程序修复AOF文件在进行启动。

appendonly no 
#yes开启AOF，no关闭AOF

appendfilename appendonly.aof
#指定AOF文件名，4.0无法通过config set 设置，只能通过修改配置文件设置。

dir /etc/redis
#RDB文件和AOF文件存放目录
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

### 实践

实践操作这里使用手动执bgrewriteaof演示重写。

![img](https://images2018.cnblogs.com/blog/1075473/201807/1075473-20180726172011427-1348771901.png)

查看日志：

![img](https://images2018.cnblogs.com/blog/1075473/201807/1075473-20180726172245449-1830645633.png)

 

## 三、RDB-AOF混合持久化

### 简介

redis4.0相对与3.X版本其中一个比较大的变化是4.0添加了新的混合持久化方式。前面已经详细介绍了AOF持久化以及RDB持久化，这里介绍的混合持久化就是同时结合RDB持久化以及AOF持久化混合写入AOF文件。这样做的好处是可以结合 rdb 和 aof 的优点, 快速加载同时避免丢失过多的数据，缺点是 aof 里面的 rdb 部分就是压缩格式不再是 aof 格式，可读性差。

### 开启混合持久化

4.0版本的混合持久化默认关闭的，通过aof-use-rdb-preamble配置参数控制，yes则表示开启，no表示禁用，默认是禁用的，可通过config set修改。

### 混合持久化过程

了解了AOF持久化过程和RDB持久化过程以后，混合持久化过程就相对简单了。

混合持久化同样也是通过bgrewriteaof完成的，不同的是当开启混合持久化时，fork出的子进程先将共享的内存副本全量的以RDB方式写入aof文件，然后在将重写缓冲区的增量命令以AOF方式写入到文件，写入完成后通知主进程更新统计信息，并将新的含有RDB格式和AOF格式的AOF文件替换旧的的AOF文件。简单的说：新的AOF文件前半段是RDB格式的全量数据后半段是AOF格式的增量数据，如下图：

![img](https://images2018.cnblogs.com/blog/1075473/201807/1075473-20180726181756270-1907770368.png)

 

### 数据恢复

当我们开启了混合持久化时，启动redis依然优先加载aof文件，aof文件加载可能有两种情况如下：

- aof文件开头是rdb的格式, 先加载 rdb内容再加载剩余的 aof。
- aof文件开头不是rdb的格式，直接以aof格式加载整个文件。

### 实践 

 开启混合持久化，并在开启后立马执行写操作，为了证实混合持久化的后半部分AOF过程

![img](https://images2018.cnblogs.com/blog/1075473/201807/1075473-20180726184949387-821527155.png)

查看日志：

![img](https://images2018.cnblogs.com/blog/1075473/201807/1075473-20180726185553432-461739380.png)

此时的aof文件已经和只开启AOF持久化文件不一样了，上半部分是RDB持久化的数据，下半部分是AOF格式数据。

![img](https://images2018.cnblogs.com/blog/1075473/201807/1075473-20180726185413299-1737324617.png)![img](https://images2018.cnblogs.com/blog/1075473/201807/1075473-20180726185422042-1609679914.png)

 

## 四、优缺点 

###  RDB

优点：

1. RDB 是一个非常紧凑（compact）的文件，体积小，因此在传输速度上比较快，因此适合灾难恢复。 
2. RDB 可以最大化 Redis 的性能：父进程在保存 RDB 文件时唯一要做的就是 `fork` 出一个子进程，然后这个子进程就会处理接下来的所有保存工作，父进程无须执行任何磁盘 I/O 操作。
3. RDB 在恢复大数据集时的速度比 AOF 的恢复速度要快。

缺点：

1. RDB是一个快照过程，无法完整的保存所以数据，尤其在数据量比较大时候，一旦出现故障丢失的数据将更多。
2. 当redis中数据集比较大时候，RDB由于RDB方式需要对数据进行完成拷贝并生成快照文件，fork的子进程会耗CPU，并且数据越大，RDB快照生成会越耗时。
3. RDB文件是特定的格式，阅读性差，由于格式固定，可能存在不兼容情况。

### AOF　

优点：

1. 数据更完整，秒级数据丢失(取决于设置fsync策略)。
2. 兼容性较高，由于是基于redis通讯协议而形成的命令追加方式，无论何种版本的redis都兼容，再者aof文件是明文的，可阅读性较好。

缺点：

1. 数据文件体积较大,即使有重写机制，但是在相同的数据集情况下，AOF文件通常比RDB文件大。
2. 相对RDB方式，AOF速度慢于RDB，并且在数据量大时候，恢复速度AOF速度也是慢于RDB。
3. 由于频繁地将命令同步到文件中，AOF持久化对性能的影响相对RDB较大，但是对于我们来说是可以接受的。

### **混合持久化**

优点：

1. 混合持久化结合了RDB持久化 和 AOF 持久化的优点, 由于绝大部分都是RDB格式，加载速度快，同时结合AOF，增量的数据以AOF方式保存了，数据更少的丢失。

缺点：

1. 兼容性差，一旦开启了混合持久化，在4.0之前版本都不识别该aof文件，同时由于前部分是RDB格式，阅读性较差

 

## 五、相关命令

aof文件检查

```
redis-check-aof /etc/redis/appendonly.aof
```

rdb文件检查

```
redis-check-rdb /etc/redis/dump.rdb
```

查看持久化信息

```
info Persistence
```

查看状态信息

```
info stats
```

 

```
复制演进中解决的问题
　　早起版本才用的sync同步方法，虽然实现了简单的主从同步，但是在从库断线或重启时无法实现部分同步，由此在2.8版本推出psync1，redis2.8的部分同步机制，有效解决了网络环境不稳定、redis执行高时间复杂度的命令引起的复制中断，从而导致全量同步。但在应对从库重启和主库故障切换的场景时，psync1还是需进行全量同步。于是在4.0新的psync得到了加强，redis4.0通过在关闭时候执行RDB快照，将复制信息保存在RDB中等到重新启动时加载RDB文件载入复制信息，通过对比复制信息启用部分复制，有效的解决了psync1情形下从库重启复制信息丢失而导致全量同步的问题。同时引入两组replid、offset，主从切换时交换两组值来实现主从故障切换时候依旧采用部分复制。

　　再次强调，在上述的过程的实现是从库不开启AOF持久化情况下,如果从库开启的AOF持久化，重启时候依然使用全量复制。

 

故障切换　
　　在实际生产环境中，在没有哨兵的主从架构里如果要重启从库，比较好的方式是先动态调配主库中的复制积压缓冲队列，调整大小应大于某个N值，N值计算公式：backlog_size = 重启从实例时长 * 主实例offset每秒写入量，这样好处在于，即使从库重启断线一段时间后再启动任然保持部分复制。调整方式通过config set repl-backlog-size <字节数>，当我们重启完成后又可以将

repl-backlog-size重新调回原有值。当然在数据量小或者重启时间短情况下，也可以直接重启从节点。　

　　当主库宕机时候，在没有哨兵情况下，需要现将从节点中的某一台提升为主库，我们需要在所有从节点中找到当前的offset最大值的从库（这样代表该库相对其他从库数据较全），然后执行slaveof no one将该从库提升为主库，最后将所有其他重库依次执行slaveof ip port（ip和port是新主库的IP地址和端口），最后完成故障切换。此外，redis4.0中这种切换任然采用部分复制进行数据同步。

 

主从配置参数
复制代码
########从库##############

slaveof <masterip> <masterport> 
#设置该数据库为其他数据库的从数据库

masterauth <master-password>
#主从复制中，设置连接master服务器的密码（前提master启用了认证）

slave-serve-stale-data yes
# 当从库同主库失去连接或者复制正在进行，从库有两种运行方式：
# 1) 如果slave-serve-stale-data设置为yes(默认设置)，从库会继续相应客户端的请求
# 2) 如果slave-serve-stale-data设置为no，除了INFO和SLAVOF命令之外的任何请求都会返回一个错误"SYNC with master in progress"

slave-priority 100
#当主库发生宕机时候，哨兵会选择优先级最高的一个称为主库，从库优先级配置默认100，数值越小优先级越高

slave-read-only yes
#从节点是否只读；默认yes只读，为了保持数据一致性，应保持默认


####主库##############

repl-disable-tcp-nodelay no
#在slave和master同步后（发送psync/sync），后续的同步是否设置成TCP_NODELAY假如设置成yes，则redis会合并小的TCP包从而节省带宽，但会增加同步延迟（40ms），造成master与slave数据不一致假如设置成no，则redis master会立即发送同步数据，没有延迟
#前者关注性能，后者关注一致性

repl-ping-slave-period 10
#从库会按照一个时间间隔向主库发送PING命令来判断主服务器是否在线，默认是10秒

repl-backlog-size 1mb
#复制积压缓冲区大小设置

repl-backlog-ttl 3600
#master没有slave一段时间会释放复制缓冲区的内存，repl-backlog-ttl用来设置该时间长度。单位为秒。

min-slaves-to-write 3
min-slaves-max-lag 10
#设置某个时间断内，如果从库数量小于该某个值则不允许主机进行写操作，以上参数表示10秒内如果主库的从节点小于3个，则主库不接受写请求，min-slaves-to-write 0代表关闭此功能。
```

哨兵(后文统称sentinel)是官方推荐的的高可用(HA)解决方案。在之前的文章中介绍过redis的主从高可用解决方案，这种方案的缺点在于当master故障时候，需要手动进行故障恢复，而sentinel是一个独立运行的进程，它能监控一个或多个主从集群，并能在master故障时候自动进行故障转移，更为理想的是sentinel本身是一个分布式系统，其分布式设计思想有点类似于zookeeper，当某个时候Master故障后，sentinel集群采用Raft算法来选取Leader，故障转移由Leader完成。而对于客户端来说，操作redis的主节点，我们只需要询问sentinel，sentinel返回当前可用的master，这样一来客户端不需要关注的切换而引发的客户端配置变更。一个典型的sentinel架构如下图：

![img](https://images2018.cnblogs.com/blog/1075473/201808/1075473-20180828145830495-31149534.png)

 

 

sentinel的主要功能：

- 监控(Monitoring): sentinel 会不断地检查Master和Slave是否运作正常。
- 通知(Notification):当被监控的某个Redis实例出现问题时, 哨兵(sentinel) 可以通过 API 向管理员或者其他应用程序发送通知。
- 自动故障迁移(Automatic failover):当一个Master不能正常工作时，sentinel)会开始一次自动故障迁移操作,它会将失效Master的其中一个Slave升级为新的Master, 并让失效Master的其他Slave改为复制新的Master; 当客户端试图连接失效的Master时,集群也会向客户端返回新Master的地址,使得集群可以使用Master代替失效Master。
- 配置中心(Configuration provider)：如果故障转移发生了，sentinel会返回新的master地址。

## 三、Sentinel集群部署

### 环境规划

　　本次部署过程中将分别部署三个哨兵节点来监控一主二从的redis集群，主从的搭建过程可以参考笔者博文《[redis系列--主从复制以及redis复制演进](https://www.cnblogs.com/wdliu/p/9407179.html)》，以下是环境规则：

- 哨兵节点：10.1.210.32:26379、10.1.210.33:26379、10.1.210.34:26379
- redis实例：10.1.210.69:6379(主)、10.1.210.69:6380(从)、10.1.210.69:6381(从)

### 安装配置

　　sentinel安装与redis安装过程一致，请参考redis系列文章，而在源码中，redis提供了参考配置示例sentinel.conf（可以使用命令grep -E -v ^# sentinel.conf 查看），如下：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
port 26379

dir /tmp

sentinel monitor mymaster 127.0.0.1 6379 2

sentinel down-after-milliseconds mymaster 30000

sentinel parallel-syncs mymaster 1

sentinel failover-timeout mymaster 180000
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

配置说明：

```
sentinel monitor mymaster 127.0.0.1 6379 2
```

 这行配置代表sentinel监控的master名字叫做mymaster(可以自己取)，地址是127.0.0.1，端口是6379。最后一个2代表当sentinel集群中有2个sentinel认为master故障时候才判定master真正不可用。官方把该参数称为quorum，在后续选举领头哨兵时候会用到，在下文将进行介绍。

```
sentinel down-after-milliseconds mymaster 30000
```

sentinel会向master发送心跳PING来确认master是否存活，如果master在“一定时间范围”内不回应PONG 或者是回复了一个错误消息，那么这个sentinel会主观地(单方面地)认为这个master已经不可用了(subjectively down, 也简称为SDOWN)。而这个down-after-milliseconds就是用来指定这个“一定时间范围”的，单位是毫秒，在这里表示30秒时间内master不回应PONG则主观不可用。

```
sentinel parallel-syncs mymaster 1
```

该配置表明在发生failover主备切换时候，最多允许多少个slave同时同步新的master。这个数字越小，完成failover所需的时间就越长，但是如果这个数字越大，就意味着越多的slave因为replication而不可用。可以通过将这个值设为 1 来保证每次只有一个slave处于不能处理命令请求的状态。

```
sentinel failover-timeout mymaster 180000
```

failover-time超时时间，当failover开始后，在此时间内仍然没有触发任何failover操作，当前sentinel将会认为此次failover失败，单位毫秒。

不难发现关于sentinel的配置都是固定格式如下：

```
sentinel <option_name> <master_name> <option_value>
```

 

### 运行Sentinel

　　启动sentinel的方式有两种，两种方式都必须指定配置文件：

```
#第一种(推荐)
redis-sentinel /path/to/sentinel.conf

#第二种
redis-server /path/to/sentinel.conf --sentinel
```

以下是笔者三个节点的配置文件，并同时拷贝到三个节点进行启动：

![img](https://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif) 10.1.210.32

```
bind 10.1.210.32 #IP地址

port 26379   #端口

dir /opt/db/redis  # 数据存储目录

daemonize yes  #后台运行

logfile /opt/db/redis/sentinel.log  #日志

sentinel monitor mymaster 10.1.210.69 6379 2

sentinel down-after-milliseconds mymaster 30000

sentinel parallel-syncs mymaster 1

sentinel failover-timeout mymaster 180000


```

![img](https://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif) 10.1.210.33

```
bind 10.1.210.33 #IP地址

port 26379   #端口

dir /opt/db/redis  # 数据存储目录

daemonize yes  #后台运行

logfile /opt/db/redis/sentinel.log  #日志

sentinel monitor mymaster 10.1.210.69 6379 2

sentinel down-after-milliseconds mymaster 30000

sentinel parallel-syncs mymaster 1

sentinel failover-timeout mymaster 180000


```

![img](https://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif) 10.1.210.34

```
bind 10.1.210.34 #IP地址

port 26379   #端口

dir /opt/db/redis  # 数据存储目录

daemonize yes  #后台运行

logfile /opt/db/redis/sentinel.log  #日志

sentinel monitor mymaster 10.1.210.69 6379 2

sentinel down-after-milliseconds mymaster 30000

sentinel parallel-syncs mymaster 1

sentinel failover-timeout mymaster 180000


```

通过redis-sentinel /opt/db/redis/sentinel.conf启动每个sentinel，以下是启动日志（可以发现sentinel自动通过master发现slave和其他sentinel）：

![img](https://images2018.cnblogs.com/blog/1075473/201808/1075473-20180828164106595-1012319053.png)

 此时，一个sentinel集群就搭建完成。

 

### sentinel相关命令

　　和redis一样，sentinel可以通过客户端使用命令操作，例如查看master状态**SENTINEL masters，**示例：

![img](https://images2018.cnblogs.com/blog/1075473/201808/1075473-20180828171153590-1963563143.png)

以下是所有命令以及解释：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
SENTINEL masters  #列出所有被监视的master，以及当前master状态
SENTINEL master <master name>  #列出指定的master
SENTINEL slaves <master name>  #列出给定master的所有slave以及slave状态
SENTINEL sentinels <master name>  #列出监控指定的master的所有sentinel
SENTINEL get-master-addr-by-name <master name>  #返回给定master名字的服务器的IP地址和端口号
SENTINEL reset <pattern>  #重置所有匹配pattern表达式的master状态
SENTINEL failover <master name>  #当msater失效时， 在不询问其他 Sentinel 意见的情况下， 强制开始一次自动故障迁移，但是它会给其他sentinel发送一个最新的配置，其他sentinel会根据这个配置进行更新
SENTINEL ckquorum <master name> #检查当前sentinel的配置能否达到故障切换master所需的数量，此命令可用于检测sentinel部署是否正常,正常返回ok
SENTINEL flushconfig #强制sentinel将运行时配置写入磁盘，包括当前sentinel状态
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

##  四、Sentinel原理

### SDOWN和ODOWN

　　在介绍sentinel原理之前，需要了解的两个概念SDOWN和ODOWN：

- SDOWN：全拼Subjectively Down，称为主观下线，指的是单个sentinel对redis实例作出的下线状态判断。
- ODOWN：全拼Objectively Down，称为客户端下线，指多个 Sentinel 实例在对同一个redis做出 SDOWN 判断，并且通过 SENTINEL is-master-down-by-addr 命令互相交流之后，得出的redis实例下线判断。（一个 Sentinel 可以通过向另一个 Sentinel 发送 SENTINEL is-master-down-by-addr 命令来询问对方是否认为给定的redis实例已下线。）

从sentinel的角度来看，如果发送了PING心跳后，在一定时间内没有收到合法的回复，就达到了SDOWN的条件。这个时间在配置中通过master-down-after-milliseconds参数配置。

当sentinel发送PING后，以下回复之一都被认为是合法的：

```
PING replied with +PONG.
PING replied with -LOADING error.
PING replied with -MASTERDOWN error.
```

其它任何回复（或者根本没有回复）都是不合法的。

从SDOWN切换到ODOWN不需要任何一致性算法，只需要一个gossip协议：如果一个sentinel收到了足够多的sentinel发来消息告诉它某个master已经down掉了，SDOWN状态就会变成ODOWN状态。如果之后master可用了，这个状态就会相应地被清理掉。

真正进行failover需要一个授权的过程，这个授权的过程即是leader选取过程，但是所有的failover都开始于一个ODOWN状态。ODOWN状态只适用于master，对于不是master的redis节点sentinel之间不需要任何协商，slaves和sentinel不会有ODOWN状态。

 

### 实现原理　　

　　一个sentinel启动时会读取配置文件，并通过sentinel monitor <master-name> <ip> <port> <quorum>配置寻找要监控的主数据库，这个配置在之前已经进行详细说明，其中master-name是由一个大小写字母、数字、和“._-”组成的数据库主库名字，为了考虑到主库的IP地址和端口可能在故障切换后发生变化，所以还需要ip和port来标示这个主库。一个哨兵可监控多个主从系统从而形成网状结构，正如在前面简介的图示一样。

　　sentinel启动后，会与监控的数据库建立两条连接,如下图（主库10.1.210.69:6379与10.1.210.32的sentinel节点两条链接）：

![img](https://images2018.cnblogs.com/blog/1075473/201808/1075473-20180829110500566-920511431.png)

这两个连接与普通客户端一样，其中一条连接用来订阅master的__sentinel__:hello频道用于获取其他监控该数据库的sentinel节点信息，另外一条用于哨兵定期向主数据库发送INFO等命令获取主库本身信息，原因在于当客户端进入订阅模式以后只能接受消息，不能发送命令，所以还需要建立一条连接。

　　与监控的主库建立连接完成后，sentinel定时执行以下操作：

1. 每10s会向主数据库和从数据库发送INFO命令；
2. 每1s向master、slave以及其他哨兵节点发送PING命令；
3. 每2s向master和slave的__sentiel__:hello频道发送自己的信息来宣布自己的存在，同时该过程也是实现哨兵之间自动发现的基础；

这三个操作贯穿了哨兵整个生命周期，非常重要，也是其原理的核心，所以以下将详细介绍该操作过程。

　　首先，sentinel启动后，向主库发送INFO命令使得sentinel可以获取当前主库的相关信息（包括运行的ID，复制信息、以及属于该主库的从库节点信息），这也是为什么在配置监控时候只需要配置监控的主库信息sentinel就自动找到其对应的从库，进而实现从库的监控。而后和每个从库同样建立两个连接，这两个连接和上文介绍的与主库的连个连接完全一致，在此之后，哨兵会每10s定时向已知所有主从发送INFO命令获取信息更新并进行相应操作，比如对新增的从库建立连接并加入监控队列、又或者是主库信息发生变化（由failover引起的）进行信息更新等。

　　接下来哨兵向master和slave的__sentinel__:hello频道发送信息与同样监控该redis示例的其他哨兵分享自己的信息。发送的消息内容为：

<哨兵地址> ,<哨兵端口>,<哨兵运行的ID>,<哨兵配置的版本>,<主库名称>,<主库地址>,<主库端口>,<主库配置版本>，该消息包含了哨兵基本信息以及监控的主库信息，当其他sentinel收到消息后会判断发消息的哨兵是不是新的哨兵，如果是则将其加入已发现的哨兵列表，并创建一个到其的连接（与数据库不同）哨兵与哨兵之间只会创建一条连接用于发送PING命令，同时sentinel会判断主数据库的配置版本，如果该版本比记录数据库版本高，则更新主数据库的数据，其作用在后续介绍。

　　实现了自动发现从数据库和其他sentinel节点后，sentinel后续要做的任务是定时监控这些已经发现的主从节点和sentinel节点是否在线。这种监控实现方式是在通过一定时间间隔发送PING命令实现，时间间隔配置通过down-after-milliseconds指定，当超过down-after-milliseconds配置的时间后，如果被PING的数据库或者sentinel未回复，则哨兵认为其**主观下线**（主观下线在上面已经介绍了），如果该节点是主库sentinel会进一步进行判断是否需要对其进行故障恢复(failover)：sentinel会发送SENTINEL is-master-down-by-addr命令询问其他sentinel节点是否也认为该主库主观下线，如果达到指定数量（在示例配置中也进行了说明，示例配置的是2）时，哨兵会认为其**客观下线，**并选取领头的哨兵（leader）进行故障恢复，选举过程后续介绍。

　　选举完零头哨兵后，领头哨兵会开始对主数据库进行故障恢复，这一过程称为failover，在选取新的master时候，sentinel会考虑以下情况：

- 跟master断开连接的时长 
- slave的优先级 （由slave-priority配置指定）
- 复制偏移量offset 
- 实例运行的id（run id）

具体的选取顺序如下：

如果一个slave跟master断开连接已经超过了down-after-milliseconds的10倍，外加master宕机的时长，那么slave就被认为不适合选举为master，计算公式如下：

```
(down-after-milliseconds * 10) + milliseconds_since_master_is_in_SDOWN_state
```

接下来会对slave进行排序 

1. 按照slave优先级进行排序，slave-priority越低，优先级就越高；
2. 如果slave priority相同，那么比较复制偏移量，offset越靠后（越大）则表明和旧的主库数据同步越接近，优先级就越高 ；
3. 如果上面两个条件都相同，那么选择一个run id最小的从库；

选出从库后，零头哨兵将向从数据库发送SLAVEOF NO ONE命令升级其为新的主库，然后在向其他从库发送SLAVEOF命令将从的主库升级到最新的主库，最后更新内部记录将已经停止的主库更新为新的主库的从库，使得当该故障的主库再次恢复时候自动以从库角色继续提供服务，从启动到故障恢复完成这一些列过程即是哨兵的工作的完整流程也是其原理所在。

### 领头哨兵选举

　　在原理中提及到了，当sentinel发现主库客观下线时候会进行领头哨兵选举进行故障恢复，其选举算法采用Raft算法，这也为什么说其设计思想类似与zookpeer，选举过程大体如下：

1. 发现主库客观下线的哨兵节点（这里称为A）向每个哨兵节点发送命令要求对方选举自己为领头哨兵（leader）；
2. 如果目标哨兵没有选举过其他人，则同意将A选举为领头哨兵；
3. 如果A发现有超过半数且超过quorum参数值的哨兵节点同意选自己成为领头哨兵，则A哨兵成功选举为领头哨兵。
4. 当有多个哨兵节点同时参与领头哨兵选举时，出现没有任何节点当选可能，此时每个参选节点等待一个随机时间进行下一轮选举，直到选出领头哨兵。

**配置版本号作用**

　　同样，在原理介绍时候提及到了master的配置版本号，当一个sentinel被授权后，它将会获得宕掉的master的一份最新配置版本号，当failover执行结束以后，这个版本号将会被用于最新的配置。因为大多数sentinel都已经知道该版本号已经被要执行failover的sentinel拿走了，所以其他的sentinel都不能再去使用这个版本号。这意味着，每次failover都会附带有一个独一无二的版本号。我们将会看到这样做的重要性。

而且，sentinel集群都遵守一个规则：如果sentinel A推荐sentinel B去执行failover，A会等待一段时间后，自行再次去对同一个master执行failover，这个等待的时间是通过failover-timeout配置项去配置的。从这个规则可以看出，sentinel集群中的sentinel不会再同一时刻并发去failover同一个master，第一个进行failover的sentinel如果失败了，另外一个将会在一定时间内进行重新进行failover，以此类推。

sentinel保证了活跃性：如果大多数sentinel能够互相通信，最终将会有一个被授权去进行failover.
sentinel也保证了安全性：每个试图去failover同一个master的sentinel都会得到一个独一无二的版本号。

 

## 五、Sentinel状态持久化

　　snetinel的状态会被持久化地写入sentinel的配置文件中。每次当收到一个新的配置时，或者新创建一个配置时，配置会被持久化到硬盘中，并带上配置的版本戳。这意味着，可以安全的停止和重启sentinel进程。下面是被重写的配置文件截图：

![img](https://images2018.cnblogs.com/blog/1075473/201808/1075473-20180829162941020-1344270216.png)

## 七、配置传播

　　一旦一个sentinel成功地对一个master进行了failover，它将会把关于master的最新配置通过广播形式通知其它sentinel，其它的sentinel则更新对应master的配置，一个faiover要想被成功实行，sentinel必须能够向选为master的slave发送SLAVE OF NO ONE命令，然后能够通过INFO命令看到新master的配置信息。

　　当将一个slave选举为master并发送SLAVE OF NO ONE`后，即使其它的slave还没针对新master重新配置自己，failover也被认为是成功了的，然后所有sentinels将会发布新的配置信息。

新配在集群中相互传播的方式，就是为什么我们需要当一个sentinel进行failover时必须被授权一个版本号的原因。

每个sentinel使用发布/订阅的方式持续地传播master的配置版本信息，配置传播的发布/订阅管道是：__sentinel__:hello，我们可以通过订阅其频道查看频道中的消息，如下：

![img](https://images2018.cnblogs.com/blog/1075473/201808/1075473-20180829165833859-1299142873.png)

　　因为每一个配置都有一个版本号，所以以版本号最大的那个为标准。例如：假设有一个名为mymaster的地址为10.1.210.69:6379。一开始，集群中所有的sentinel都知道这个地址，于是为mymaster的配置打上版本号1。一段时候后mymaster死了，有一个sentinel被授权用版本号2对其进行failover。如果failover成功了，假设地址改为了10.1.210.69:6380，此时配置的版本号为2，进行failover的sentinel会将新配置广播给其他的sentinel，由于其他sentinel维护的版本号为1，发现新配置的版本号为2时，版本号变大了，说明配置更新了，于是就会采用最新的版本号为2的配置。

