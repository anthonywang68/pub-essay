---
title: mysql新版本，坑
date: 2019-11-12 22:34:13
tags:
---

# mysql8之坑

<!--more-->

#### 一、具体"坑"

1、修改密码和修改加密方式

　　mysql> `ALTER USER 'root'@'localhost' IDENTIFIED BY '新密码' PASSWORD EXPIRE NEVER;`

　　mysql> ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '新密码';

　　mysql> `FLUSH PRIVILEGES;`

2、默认表名不区分大小写



#### 二、区别于其他版本的安装方式

1、在初始化数据库（生成data文件夹）之前新建my.ini文件在mysql的安装目录，内容如下：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
[mysqld]
# basedir=C:\mysql-8.0.17-winx64 
# datadir=C:\mysql-8.0.17-winx64\data
# sql_mode=NO_ENGINE_SUBSTITUTION,STRICT_TRANS_TABLES
port=3306

character-set-server=utf8mb4
collation-server=utf8mb4_0900_ai_ci

# 设置协议认证方式(重点啊)
default_authentication_plugin=mysql_native_password

# 大小写敏感，lower_case_table_names=1是不敏感
lower_case_table_names=2

[client]
default-character-set=utf8mb4

[mysql]
default-character-set=utf8mb4
```

```
一、创建用户和授权

在mysql8.0创建用户和授权和之前不太一样了，其实严格上来讲，也不能说是不一样,只能说是更严格,mysql8.0需要先创建用户和设置密码,然后才能授权。

?

#先创建一个用户
create user 'hong'@'%' identified by '123123';
 
#再进行授权
grant all privileges on *.* to 'hong'@'%' with grant option;
如果还是用原来5.7的那种方式，会报错误：

grant all privileges on *.* to 'sroot'@'%' identified by '123123';

ERROR 1064 (42000): You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near 'identified by '123123'' at line 1

 二、MySQL8.0 的远程链接

MySQL8.0 安装完成后出现无法远程链接的现象，这是因为MySQL8.0 只支持 localhost 访问，我们必须设置一下才可以远程访问。

具体设置步骤如下：

① 登录MySQL

执行命令为：mysql -u root -p 

回车后输入密码

② 选择 mysql 数据库

执行命令为：use mysql;

查看mysql 数据库中存储的用户信息的 user 表。

③ 查看mysql 数据库的 user 表中当前 root 用户的相关信息

执行命令为：select host,user,authentication_string,plugin from user;

执行完命令后显示一个表格， root 用户的 host默认显示的 localhost，说明只支持本地访问，不允许远程访问。

④ 更改 host 的默认配置

执行命令为：update user set host='%' where user='root';
⑤ 刷新

执行命令为：flush privileges;

之前以为这样就行了，然后呢，在用navicat进行mysql的远程连接时，出现了弹窗报错：


出现这个原因是mysql8 之前的版本中加密规则是mysql_native_password,而在mysql8之后,加密规则是caching_sha2_password, 解决问题方法有两种,一种是升级navicat驱动,一种是把mysql用户登录密码加密规则还原成mysql_native_password. 我用是第二种方式 ：
?

ALTER USER 'root'@'%' IDENTIFIED BY 'password' PASSWORD EXPIRE NEVER; #修改加密规则 
ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY 'password';#更新一下用户的密码
FLUSH PRIVILEGES; #刷新权限 
问题就解决了。

mysql8.0用户密码设置注意事项

在MySQL 8.0.11中，caching_sha2_password是默认的身份验证插件，而不是以往的mysql_native_password。有关此更改对服务器操作的影响以及服务器与客户端和连接器的兼容性的信息，请参阅caching_sha2_password作为首选的身份验证插件。(翻译自https://dev.mysql.com/doc/refman/8.0/en/caching-sha2-pluggable-authentication.html）

今天在新服务上配置安装mysql8.0.11时，像往常一样设置mysql密码，设置成功后在shell下输入mysql -u root -p，再输入密码能正常进入，但在phpmyadmin或直接用http://php.net/manual/zh/mysqli.real-connect.php上的连接，均提示无法连接，具体报错信息为

mysqli_real_connect(): The server requested authentication method unknown to the client [sha256_password]

搜了一圈，找到官方文档才发现从8.0.11版本起，不再像mysql5.7及以前版本那样，设置用户密码时默认的验证方式为caching_sha2_password，如果发现升级mysql8.0.11后原有的程序不能连接mysql，可迅速在mysql command line client客户端用下面的命令设置成mysql5.7及以前版本的密码验证方式，同时MYSQL8.0.11下修改密码的方式与原先也不大一样，原先的部分修改密码的命令在mysql8.0.11下不能使用。

?

> use mysql 
 
> ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '你的密码'; 
 
> FLUSH PRIVILEGES; 

```

