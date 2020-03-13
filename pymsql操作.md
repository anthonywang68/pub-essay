---
title: pymsql操作
date:
tags:
---

# pymysql的安装

```
pip install PyMySQL
```

## 连接数据库

<!--more-->

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

 

```
import pymysql

db = pymysql.connect("数据库ip","用户","密码","数据库" ) # 打开数据库连接
cursor.execute("SELECT VERSION()")                    # 使用 execute() 方法执行 SQL 查询
data = cursor.fetchone()                              # 使用 fetchone() 方法获取单条数据
print ("Database version : %s " % data)
db.close()                                            # 关闭数据库连接
```

 

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

## 创建表操作

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

 

```
import pymysql
 
# 打开数据库连接
db = pymysql.connect("localhost","testuser","test123","TESTDB" )
 
# 使用 cursor() 方法创建一个游标对象 cursor
cursor = db.cursor()
 
# 使用 execute() 方法执行 SQL，如果表存在则删除
cursor.execute("DROP TABLE IF EXISTS EMPLOYEE")
 
# 使用预处理语句创建表
sql = """CREATE TABLE EMPLOYEE (
         FIRST_NAME  CHAR(20) NOT NULL,
         LAST_NAME  CHAR(20),
         AGE INT,  
         SEX CHAR(1),
         INCOME FLOAT )"""
 
cursor.execute(sql)
 
# 关闭数据库连接
db.close()
```

 

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

## 插入操作

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

 

```
import pymysql
 
# 打开数据库连接
db = pymysql.connect("localhost","testuser","test123","TESTDB" )
 
# 使用cursor()方法获取操作游标 
cursor = db.cursor()
 
# SQL 插入语句
sql = """INSERT INTO EMPLOYEE(FIRST_NAME,
         LAST_NAME, AGE, SEX, INCOME)
         VALUES ('Mac', 'Mohan', 20, 'M', 2000)"""
try:
   cursor.execute(sql) # 执行sql语句
   db.commit()         # 提交到数据库执行
except:
   db.rollback()       # 如果发生错误则回滚
 
# 关闭数据库连接
db.close()
```

 

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

## 查询操作

Python查询Mysql使用 fetchone() 方法获取单条数据, 使用fetchall() 方法获取多条数据。

- **fetchone():** 该方法获取下一个查询结果集。结果集是一个对象
- **fetchall():** 接收全部的返回结果行.
- **rowcount:** 这是一个只读属性，并返回执行execute()方法后影响的行数。

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

 

```
import pymysql
 
# 打开数据库连接
db = pymysql.connect("localhost","testuser","test123","TESTDB" )
 
# 使用cursor()方法获取操作游标 
cursor = db.cursor()
 
# SQL 查询语句
sql = "SELECT * FROM EMPLOYEE \
       WHERE INCOME > %s" % (1000)
try:
   
   cursor.execute(sql)# 执行SQL语句
   results = cursor.fetchall()# 获取所有记录列表
   for row in results:
      fname = row[0]
      lname = row[1]
      age = row[2]
      sex = row[3]
      income = row[4]
       # 打印结果
      print ("fname=%s,lname=%s,age=%s,sex=%s,income=%s" % \
             (fname, lname, age, sex, income ))
except:
   print ("Error: unable to fetch data")
 
# 关闭数据库连接
db.close()
```

 

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

## 更新操作

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

 

```
import pymysql
 
# 打开数据库连接
db = pymysql.connect("localhost","testuser","test123","TESTDB" )
 
# 使用cursor()方法获取操作游标 
cursor = db.cursor()
 
# SQL 更新语句
sql = "UPDATE EMPLOYEE SET AGE = AGE + 1 WHERE SEX = '%c'" % ('M')
try:
   cursor.execute(sql)  # 执行SQL语句
   db.commit()          # 提交到数据库执行
except
   db.rollback()        # 发生错误时回滚
 
# 关闭数据库连接
db.close()
```

 

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

## 删除操作

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

 

```
import pymysql
 
# 打开数据库连接
db = pymysql.connect("localhost","testuser","test123","TESTDB" )
 
# 使用cursor()方法获取操作游标 
cursor = db.cursor()
 
# SQL 删除语句
sql = "DELETE FROM EMPLOYEE WHERE AGE > %s" % (20)
try
   cursor.execute(sql)  # 执行SQL语句
   db.commit()          # 提交修改
except
   db.rollback()        # 发生错误时回滚# 关闭连接
db.close()
```

 

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

## 数据备份

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

 

```
#语法：
# mysqldump -h 服务器 -u用户名 -p密码 数据库名 > 备份文件.sql

#示例：
#单库备份
mysqldump -uroot -p123 db1 > db1.sql
mysqldump -uroot -p123 db1 table1 table2 > db1-table1-table2.sql

#多库备份
mysqldump -uroot -p123 --databases db1 db2 mysql db3 > db1_db2_mysql_db3.sql

#备份所有库
mysqldump -uroot -p123 --all-databases > all.sql 
```

 

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

## 数据恢复

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

 

```
#方法一：
[root@egon backup]# mysql -uroot -p123 < /backup/all.sql

#方法二：
mysql> use db1;
mysql> SET SQL_LOG_BIN=0;   #关闭二进制日志，只对当前session生效
mysql> source /root/db1.sql
```

 

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

## 事务和锁

```
begin;  # 开启事务
select * from emp where id = 1 for update;  # 查询id值，for update添加行锁；
update emp set salary=10000 where id = 1; # 完成更新
commit; # 提交事务
```

