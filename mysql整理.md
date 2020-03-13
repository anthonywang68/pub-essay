---
title: mysql整理
date: 
tags:数据库
---

数据库整理，未完待续。。。

<!--more-->

**一.概述**

　　1. 什么是数据库?

　　　　答: 储存数据的仓库, 如: 在ATM的事例中创建的一个db 目录, 称为数据库

　　2. 什么是Mysql, Oracle, SQLite, Access, MS SQL Server等 ?

　　　　答: 他们均是一个软件, 都有两个主要的功能:

　　　　　　a.将数据保存到文件或内存

　　　　　　b.接受特定的命令, 然后对文件进行相应的操作,统称为数据库管理系统(DBMS, Database Management System)

　　3. 什么是SQL?

　　　　答: 是一种专门用来一数据库通信的语言(Structured Query Language),Mysql等软件可以接受命令,并作出相应的操作,由于命令中可以删除文件,获取文件内容等操作,对于编写的命令就是SQL语句.

　　4.数据库服务器,数据管理系统,数据库,表与记录的关系?

　　　　答:

　　　　　　数据库服务器: 运行数据库管理软件

　　　　　　数据库管理软件: 管理数据库

　　　　　　数据库: 既文件夹,用来组织文件/表

　　　　　　表: 既文件,用来存放多行内容/多条记录　　　

**二.下载安装**

　　想要使用Mysql 来储存并操作数据,需要做几件事情:

　　　　a.安装Mysql服务端

　　　　b.安装Mysql客户端

　　　　c. 客户端连接服务端

　　　　d. 客户端发送命令给服务端mysql 服务的接受命令并执行相应操作.

　　　**window版本**

　　　　1. 下载

```
下载地址: 
            http://dev.mysql.com/downloads/mysql/

    
            MySQL Community Server 5.7.16 
```

　　　　2. 解压

　　　　　　如果想要让MySQL安装在指定目录，那么就将解压后的文件夹移动到指定目录，如：C:\mysql-5.7.16-winx64

　　　　3. 初始化

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
MySQL解压后的 bin 目录下有一大堆的可执行文件，执行如下命令初始化数据：


    cd c:\mysql-5.7.16-winx64\bin

 

    mysqld --initialize-insecure
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　　　4. 启动MySQL服务

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
# 进入可执行文件目录

cd c:\mysql-5.7.16-winx64\bin

 

# 启动MySQL服务

mysqld
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　　　5.启动MySQL客户端并连接MySQL服务

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
由于初始化时使用的【mysqld --initialize-insecure】命令，其默认未给root账户设置密码


# 进入可执行文件目录

cd c:\mysql-5.7.16-winx64\bin


# 连接MySQL服务器

mysql -u root -p

 
# 提示请输入密码，直接回车
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　　　在提供一个更方便的方法利用

　　　　net start mysql

　　　　net stop mysql

![img](https://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif) 命令启动服务端

　　**linux版本**

![img](https://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif) linnux~

　　**可视化工具Navicat**

　　　　　**以图形界面的方式操作MySQL数据库**

```
官网下载：https://www.navicat.com/en/products/navicat-for-mysql
网盘下载：https://pan.baidu.com/s/1bpo5mqj
```

　　　　

![img](https://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif) 需掌握技能

 

**三.数据库操作**

　　1, 显示数据库

```
SHOW DATABASE;
```

　　默认数据库:

　　　　mysql - 用户权限相关数据

　　　　test - 用于用户测试数据

　　　　information_schema - MySQL本身架构相关数据

　　2, 创建数据库

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
# utf-8

CREATE DATABASE 数据库名称 DEFAULT CHARSET utf8 COLLATE utf8_general_ci;

 

# gbk

CREATE DATABASE 数据库名称 DEFAULT CHARACTER SET gbk COLLATE gbk_chinese_ci;
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　3. 使用数据库

```
USE db_name;
```

　　4. 用户管理

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
创建用户
    create user '用户名'@'IP地址' identified by '密码';

删除用户
    drop user '用户名'@'IP地址';

修改用户
    rename user '用户名'@'IP地址'; to '新用户名'@'IP地址';;

修改密码
    set password for '用户名'@'IP地址' = Password('新密码')

 
PS：用户权限相关数据保存在mysql数据库的user表中，所以也可以直接对其进行操作（不建议）
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　5. 授权管理

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
管理员下:
    
show grants for '用户'@'IP地址'                  -- 查看权限

grant  权限 on 数据库.表 to   '用户'@'IP地址'      -- 授权

revoke 权限 on 数据库.表 from '用户'@'IP地址'      -- 取消权限
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

![img](https://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif) 关于权限

![img](https://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif) 关于数据库

![img](https://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif) 关于用户和IP

![img](https://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif) 实例

需要注意的:

```
# 刷新
    flush privileges,  将数据读取到内存中, 从而生效
```

![img](https://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif) 忘记密码解决办法

 

**四.数据表基本操作**

　　1, 创建表

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
create table 表名(

    列名  类型  是否可以为空，

    列名  类型  是否可以为空

)ENGINE=InnoDB DEFAULT CHARSET=utf8
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

![img](https://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif) 是否可以为空

![img](https://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif) 默认值

![img](https://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif) 自动增加

![img](https://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif) 主键

![img](https://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif) 外键

　　2, 删除表

```
drop table 表名
```

　　3, 清空表

```
 delect from 表名
 truncate table 表名
```

　　4, 修改表 alter

![img](https://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif) 修改表相关操作

　　5. 基本数据类型

　　　　MySQL的数据类型大概分为数值, 时间, 字符串

![img](https://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif) View Code

　　　　*二进制数据：TinyBlob、Blob、MediumBlob、LongBlob*

**五.表内容操作(记录)**

　　1, 增

```
insert into 表 (列名,列名...) values (值,值,值...)
insert into 表 (列名,列名...) values (值,值,值...),(值,值,值...)
insert into 表 (列名,列名...) select (列名,列名...) from 表
```

 

　　2, 删

```
delete from 表(.. , ..)
delecy from 表 values(.. , ..)
delete from 表 where id＝1 and name＝'alex'
```

 

　　3,改

```
update 库.表 set name ＝ 'konghui' where id>1
```

 

　　4,查

```
select * from 表
select * from 表 where id > 1
select nid,name,gender as gg from 表 where id > 1
```

 

　　5, 其他查询相关(重点)

　　关键字的 优先级:

　　　　from　　找到表

　　　　where　　条件1,取出一条记录

　　　　group by　　将取出的一条记录,进行分组, 如果没有group by , 则整体作为一组,只能查看当前字段,如果想查看组内信息，需要借助于聚合函数

　　　　having　　条件2, 将分组的结果进行having 过滤, 必须在group by 之后用

　　　　select　　查

　　　　distinct　　去重

　　　　order by　　将结果按条件排序

　　　　limit　　　限制结果的显示条数

 

![img](https://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif) 相关操作

 

------

　

　　　　　　快速记忆表三张...

![img](https://img2018.cnblogs.com/blog/1479869/201811/1479869-20181124154901412-945246212.png)

![img](https://img2018.cnblogs.com/blog/1479869/201811/1479869-20181124154927146-102852665.png)

![img](https://img2018.cnblogs.com/blog/1479869/201811/1479869-20181124155658841-2057433347.png)

**一. 视图**

　　视图是一个虚拟表,其本质是根据SQL语句获取动态的数据集,并为其命名,用户使用时只需使用名称即可获取结果集, 并可以将其当做表来使用.

![img](https://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif) 临时表查询

　　1. 创建视图

![img](https://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif) View Code

　　2. 删除视图

```
--格式：DROP VIEW 视图名称
drop view v1
```

　　3. 修改视图

![img](https://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif) View Code

　　4. 使用视图

　　使用视图时，将其当作表进行操作即可，由于视图是虚拟表，所以无法使用其对真实表进行创建、更新和删除操作，仅能做查询用。　

```
select * from v1;
```

 

**二. 触发器**

　　对某个表进行【增/删/改】操作的前后如果希望触发某个特定的行为时，可以使用触发器，触发器用于定制用户对表的行进行【增/删/改】前后的行为。

　　1. 创建基本语法

![img](https://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif) View Code

 

![img](https://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif) 插入前触发器

 

![img](https://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif) 插入后触发器

　　NEW表示即将插入的数据行，OLD表示即将删除的数据行。

　　2, 删除触发器

```
 DORP TRIGGER   tri_after_insert_tb1;
```

　　3, 使用触发器

　　触发器无法由用户直接调用,而知由于对表的(增删改)操作被动触发

```
insert into tb1(num) values(666)
```

**三. 存储过程**

　　存储过程是一个SQL语句集合，当主动去调用存储过程时，其中内部的SQL语句会按照逻辑执行。

　　1, 创建存储过程

![img](https://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif) 无 参数存储过程

　　　对于存储过程，可以接收参数，其参数有三类：

　　　　in          仅用于传入参数用

　　　　out        仅用于返回值用

　　　　inout     既可以传入又可以当作返回值

![img](https://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif) 有参数存储过程

![img](https://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif) View Code

 

　　2, 删除存储过程

```
drop procedure proc_name;
```

 

　　3, 执行储存过程

![img](https://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif) View Code

 

![img](https://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif) pymysql执行过程

 

**四. 函数**

![img](https://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif) 部分内置函数

　　1,自定义函数

![img](https://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif) View Code

　　2, 删除函数

```
drop function func_name;
```

　　  3, 执行函数

![img](https://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif) View Code

 

**五. 事务**

　　事务用于将某些操作的多个SQL作为原子性操作，一旦有某一个出现错误，即可回滚到原来的状态，从而保证数据库数据完整性。

　　set @i =0;
　　call p1(@i);
　　select @i;

![img](https://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif) View Code

 

**六. 索引**　

　　索引，是数据库中专门用于帮助用户快速查询数据的一种数据结构。类似于字典中的目录，查找字典内容时可以根据目录查找到数据的存放位置，然后直接获取即可。

 

```
                        30

            10                        40

       5         15            35             66

    1   6     11    19      21      39     55     100
```

MySQL中常见索引有：

- 普通索引
- 唯一索引
- 主键索引
- 组合索引

　　1、普通索引

　　　　普通索引仅有一个功能：加速查询

![img](https://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif) 创建索引

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
创建索引
  create index index_name on table_name(column_name) ;     
删除
 drop index_name on table_name;
查看
show index from table_name;
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　注意：对于创建索引时如果是BLOB 和 TEXT 类型，必须指定length。

　　　　　　create index ix_extra on in1(extra(32));

　　2, 唯一索引

　　　　加速查询和唯一约束

![img](https://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif) View Code

　　3, 主键索引

　　　　主键有两个功能：加速查询 和 唯一约束（不可含null）

![img](https://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif) View Code

　　4, 组合索引

　　　　组合索引是将n个列组合成一个索引

　　　　其应用场景为：频繁的同时使用n列来进行查询，如：where n1 = 'alex' and n2 = 666。

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
       (1)避免使用select *
       (2)count(1)或count(列) 代替count(*)
       (3)创建表时尽量使用char代替varchar
       (4)表的字段顺序固定长度的字段优先
       (5)组合索引代替多个单列索引（经常使用多个条件查询时）
       (6)尽量使用短索引 （create index ix_title on tb(title(16));特殊的数据类型 text类型）
       (7)使用连接（join）来代替子查询
       (8)连表时注意条件类型需一致
       (9)索引散列（重复少）不适用于建索引，例如：性别不合适
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)




![img](https://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif) 创建

　　　　如上创建组合索引之后，查询：

- - 　　name and email  -- 使用索引
  - 　　name                 -- 使用索引
  - 　　email                 -- 不使用索引

　　　　注意：对于同时搜索n个条件时，组合索引的性能好于多个单一索引合并。

![img](https://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif) 创建300万条数据

![img](https://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif) 测试

 

**七. 其他**

　　1, 条件语句

![img](https://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif) View Code

　　2, 循环语句

![img](https://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif) while

![img](https://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif) repeat

![img](https://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif) loop

　　3, 动态执行SQL语句

![img](https://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif) View Code

 　　4, 分页性能相关方案

![img](https://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif) 每10条取一次

![img](https://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif) 上一页 下一页

　　5, 慢日志记录

　　开启慢查询日志，可以让MySQL记录下查询超过指定时间的语句，通过定位分析性能的瓶颈，才能更好的优化数据库系统的性能。

![img](https://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif) View Code

　　6, 执行计划

　　explain + 查询SQL - 用于显示SQL执行信息参数，根据参考信息可以进行SQL优化

![img](https://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif) 参数说明











```

```