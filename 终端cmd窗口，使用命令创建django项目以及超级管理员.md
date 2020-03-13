---
title: 终端cmd窗口，使用命令创建django项目以及超级管理员
date: 2019-09-22 00:26:25
tags:
---

记录我在命令行窗口成功创建项目的具体操作步骤：

(后面开发新项目有xadmin操作，后续再整理进笔记里，持续更新ing。。。)

<!--more-->

1、终端先进入你要放项目的目录。

**C:\Users\Administrator>d:**

在命令行输入:django-admin startproject 项目名  。回车，此时创建了一个项目。

例：django-admin startproject mysit

**D:\>django-admin startproject mysite**

2、创建好了一个diango项目，然后再需要创建自己的一个应用项目，比如叫blog。命令行同样需要切换到mysite目录下。

D:\>cd

D:\>cd mysite

在命令行输入:python manage.py startapp blog  。回车，此时创建了一个应用项目。

D:\mysite>python manage.py startapp blog

 

3、项目创建好了。就可以启动项目了 。启动项目在pycharm的终端那里输入：python manage.py runserver 8080 就启动项目了。其中runserver后面应该是要加ip地址和端口的，但是如果不写，代表本机ip，端口自己定义一个，比如这里定义8080。

D:\mysite>python manage.py runserver 8080
Performing system checks...

System check identified no issues (0 silenced).

You have 13 unapplied migration(s). Your project may not work properly until you apply the migrations for app(s): admin,
auth, contenttypes, sessions.
Run 'python manage.py migrate' to apply them.
May 21, 2019 - 22:04:25
Django version 1.11.9, using settings 'mysite.settings'
Starting development server at http://127.0.0.1:8080/
Quit the server with CTRL-BREAK.
[21/May/2019 22:06:15] "GET / HTTP/1.1" 200 1716
Not Found: /favicon.ico
[21/May/2019 22:06:15] "GET /favicon.ico HTTP/1.1" 404 1962

 

如何创建Django超级用户的流程：

 

首选创建一个新用户，用来登录Django管理网站，进入manage.py目录下，使用如下命令来进行创建：
\>>python manage.py createsuperuser
接下来输入用户名称：
\>>Username(leave bkank to use 'administrator'): root
然后是输入邮箱（QQemail等都可以）：
\>>Email address：（输入你的邮箱账号）

输入密码（输入密码时不会显示出来，并且长度必须超过八位才行）：
\>>Password：********
\>>Password(again)：********

当两次密码输入相同且超过八位的时候，就会提示创建超级用户成功：
\>>Superuser created successfully.

再次运行你的服务，输入账号和密码就可以成功登陆了：
\>>python manage.py runserver