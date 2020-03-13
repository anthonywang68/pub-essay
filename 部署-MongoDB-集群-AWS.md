---
title: 部署 MongoDB 集群
date: 2019-11-01 21:31:13
tags:
---

开始前进入su模式，关闭防火墙systemctl stop firewalld.service   重启生效：systemctl disable firewalld.service
以下配置文件中id除非是0.0.0.0的，其他都需要根据自身情况来修改

<!--more-->

下载：mongodb： curl -O https://fastdl.mongodb.org/linux/mongodb-linux-x86_64-3.4.6.tgz

解压：tar -xzvf mongodb-linux-x86_64-3.4.6.tgz -C /data/local/

改名：mv mongodb-linux-x86_64-3.4.6 mongodb

分别在三个机器创建以下文件
    mkdir -p /data/local/mongodb/conf
    mkdir -p /data/local/mongodb/mongos/log
    mkdir -p /data/local/mongodb/config/data
    mkdir -p /data/local/mongodb/config/log
    mkdir -p /data/local/mongodb/shard1/data
    mkdir -p /data/local/mongodb/shard1/log
    mkdir -p /data/local/mongodb/shard2/data
    mkdir -p /data/local/mongodb/shard2/log
    mkdir -p /data/local/mongodb/shard3/data
    mkdir -p /data/local/mongodb/shard3/log


配置环境变量

    # 内容
    export MONGODB_HOME=/dev/local/mongodb
    export PATH=$MONGODB_HOME/bin:$PATH
    # 使立即生效    
    source /etc/profile


2、config server配置服务器

    添加配置文件
        命令：vi /data/local/mongodb/conf/config.conf
    配置文件内容如下
pidfilepath = /data/local/mongodb/config/log/configsrv.pid
dbpath = /data/local/mongodb/config/data
logpath = /data/local/mongodb/config/log/congigsrv.log
logappend = true
 bind_ip = 0.0.0.0
port = 21000
fork = true
#declare this is a config db of a cluster;
configsvr = true
#副本集名称
replSet=configs
#设置最大连接数
maxConns=20000
            注：必须关闭防火墙

启动三台服务器的config server

    命令：mongod -f /data/local/mongodb/conf/config.conf
        注：如果全局变量没执行可以到bin目录下 ./mongod -f /data/local/mongodb/conf/config.conf

登录任意一台配置服务器，初始化配置副本集
    
    命令：mongo --port 21000
设置config变量命令如下

config = {
...    _id : "configs",
...     members : [
...         {_id : 0, host : "10.3.15.199:21000" },
...         {_id : 1, host : "10.3.15.198:21000" },
...         {_id : 2, host : "10.3.15.197:21000" }
...     ]
... }
               初始化副本集命令：rs.initiate(config)


3、配置分片副本集(三台机)
vi /data/local/mongodb/conf/shard1.conf
第一个刀片副本集文件内容如下：

pidfilepath = /data/local/mongodb/shard1/log/shard1.pid
dbpath = /data/local/mongodb/shard1/data
logpath = /data/local/mongodb/shard1/log/shard1.log
logappend = true

bind_ip = 0.0.0.0
port = 27001
fork = true

#打开web监控
httpinterface=true
rest=true

#副本集名称
replSet=shard1

#declare this is a shard db of a cluster;
shardsvr = true

#设置最大连接数
maxConns=20000
    
    启动三台机器服务器命令：./mongod -f /data/local/mongodb/conf/shard1.conf

登陆非arbiterOnly: true ID的任意机器启动以下命令 (如以下id中24.175不能启动)

命令：mongo --port 27001

命令：use admin

命令：

config = {
...    _id : "shard1",
...     members : [
...         {_id : 0, host : "10.3.15.199:27001" },
...         {_id : 1, host : "10.3.15.198:27001" },
...         {_id : 2, host : "10.3.15.197:27001" , arbiterOnly: true }
...     ]
... }

初始化副本集命令：rs.initiate(config);

打开第二个刀片副本集conf：vi /data/local/mongodb/conf/shard2.conf
第二个刀片副本集配置文件如下：


pidfilepath = /data/local/mongodb/shard2/log/shard2.pid
dbpath = /data/local/mongodb/shard2/data
logpath = /data/local/mongodb/shard2/log/shard2.log
logappend = true

bind_ip = 0.0.0.0
port = 27002
fork = true

#打开web监控
httpinterface=true
rest=true

#副本集名称
replSet=shard2

#declare this is a shard db of a cluster;
shardsvr = true

#设置最大连接数
maxConns=20000

    启动三个服务器：./mongod -f /data/local/mongodb/conf/shard2.conf

登陆非arbiterOnly: true的任意机子初始化初始化副本

命令：mongo --port 27002
命令：use admin
命令：
config = {
...    _id : "shard2",
...     members : [
...         {_id : 0, host : "10.3.15.199:27002"  , arbiterOnly: true },
...         {_id : 1, host : "10.3.15.198:27002" },
...         {_id : 2, host : "10.3.15.197:27002" }
...     ]
... }

命令：rs.initiate(config);

设置第三个刀片副本集
命令：vi /data/local/mongodb/conf/shard3.conf
第三个副本集配置文件内容
pidfilepath = /data/local/mongodb/shard3/log/shard3.pid
dbpath = /data/local/mongodb/shard3/data
logpath = /data/local/mongodb/shard3/log/shard3.log
logappend = true

bind_ip = 0.0.0.0
port = 27003
fork = true

#打开web监控
httpinterface=true
rest=true

#副本集名称
replSet=shard3

#declare this is a shard db of a cluster;
shardsvr = true

#设置最大连接数
maxConns=20000

    启动三台服务器：./mongod -f /data/local/mongodb/conf/shard3.conf


登陆非arbiterOnly: true  ID的机子初始化副本集

命令：mongo --port 27003
命令：use admin
命令：
config = {
...    _id : "shard3",
...     members : [
...         {_id : 0, host : "10.3.15.199:27003" },
...         {_id : 1, host : "10.3.15.198:27003" , arbiterOnly: true},
...         {_id : 2, host : "10.3.15.197:27003" }
...     ]
... }
初始化命令：rs.initiate(config);


4、配置路由服务器 mongos

三台机打开：vi /data/local/mongodb/conf/mongos.conf

配置文件内容：
pidfilepath = /data/local/mongodb/mongos/log/mongos.pid
logpath = /data/local/mongodb/mongos/log/mongos.log
logappend = true

bind_ip = 0.0.0.0
port = 20000
fork = true

#监听的配置服务器,只能有1个或者3个 configs为配置服务器的副本集名字
configdb = configs/10.2.5.99:21000,10.2.5.98:21000,10.2.5.97:21000

#设置最大连接数
maxConns=20000

启动三台服务器的mongos server命令： ./mongos -f /data/local/mongodb/conf/mongos.conf

5、启动分片
登陆任意一台mongos
命令：mongo --port 20000
命令：use  admin
配置文件内容：
sh.addShard("shard1/10.3.15.199:27001,10.2.5.99:27001,10.3.15.199:27001")
sh.addShard("shard2/10.3.15.198:27002,10.2.5.98:27002,10.3.15.198:27002")
sh.addShard("shard3/10.3.15.197:27003,10.2.5.97:27003,10.3.15.197:27003")
查看状态命令：sh.status()

启动分片方法
db.runCommand( { enablesharding :"MongoFile"});             注：其中 MongoFile为库名，table为表名
db.runCommand( { shardcollection : "MongoFile.table",key : {id: 1} } )

---搭建成功---


         测试方法

1.进入mongoDB
    命令为：mongo --port 20000

2.新建数据库且分片
命令：use 502
switched to db 502
命令：sh.enableSharding("502")

3.创建索引
命令：db.table1.ensureIndex({"id":1})

4.表分片
命令：sh.shardCollection("502.table1",{"id":1})
查看表状态：
    <pre name="code" class="plain">mongos> sh.status()

5.存储数据
命令：for(var i=1;i<=1000000;i++){
...  db.table1.save({"id":i,"x":Math.random(),"name":"xubo","time":"20150819","ops":"testinserttimes"});
...  }
WriteResult({ "nInserted" : 1 })

6.查看每个分片数据总数
命令： db.table1.stats()

往库插数据
for (var i = 1; i <= 10000; i++) db.table1.save({id:i,"test1":"testval1"});


启动mongodb服务
mongod -f /data/local/mongodb/conf/config.conf
mongod -f /data/local/mongodb/conf/shard1.conf
mongod -f /data/local/mongodb/conf/shard2.conf
mongod -f /data/local/mongodb/conf/shard3.conf
mongos -f /data/local/mongodb/conf/mongos.conf

关闭mongodb服务
./mongo --port 20000
use admin
db.shutdownServer()