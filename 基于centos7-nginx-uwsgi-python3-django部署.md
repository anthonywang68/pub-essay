---
title: 基于centos7+nginx+uwsgi+python3+django部署
date: 2019-09-24 15:11:50
tags:
---



1，**详细步骤(下面步骤都是ROOT权限执行):**

<!--more-->

**一、更新系统软件包**

```
yum update -y
```

**二、安装软件管理包和可能使用的依赖**

```
yum -y groupinstall "Development tools"yum install openssl-devel bzip2-devel expat-devel gdbm-devel readline-devel sqlite-devel
```

**三、下载Pyhton3到/usr/local 目录**

```
wget https://www.python.org/ftp/python/3.6.3/Python-3.6.3.tgz
```

解压

```
tar -zxvf Python-3.6.3.tgz
cd Python-3.6.3
```

编译安装到指定路径

```
./configure --prefix=/usr/local/python3
```

/usr/local/python3 路径可以自己指定，自己记着就行，下边要用到。

安装python3

```
makemake install
```

安装完成之后 建立软链接 添加变量 方便在终端中直接使用python3

```
ln -s /usr/local/python3/bin/python3.6 /usr/bin/python3
```

Python3安装完成之后pip3也一块安装完成，不需要再单独安装
一样建立软链接

```
ln -s /usr/local/python3/bin/pip3.6 /usr/bin/pip3
```

**四、查看Python3和pip3安装情况**

![img](https://bbsmax.ikafan.com/static/L3Byb3h5L2h0dHBzL3d3dy5kamFuZ28uY24vbWVkaWEvdXBpbWcvdGltZ18yMDE4MDcwOTIyMDgxM18yMzEuanBn.jpg)

**五、安装virtualenv ，建议大家都安装一个virtualenv，方便不同版本项目管理。**

```
pip3 install virtualenv
```

建立软链接

```
ln -s /usr/local/python3/bin/virtualenv /usr/bin/virtualenv
```

安装成功在根目录下建立两个文件夹，主要用于存放env和网站文件的。(个人习惯，其它人可根据自己的实际情况处理)

```
mkdir -p /data/envmkdir -p /data/wwwroot
```

**六、切换到/data/env/下，创建指定版本的虚拟环境。**

```
virtualenv --python=/usr/bin/python3 pyweb
```

然后进入/data/env/pyweb/bin

启动虚拟环境：

```
source activate
```

![img](https://bbsmax.ikafan.com/static/L3Byb3h5L2h0dHBzL3d3dy5kamFuZ28uY24vbWVkaWEvdXBpbWcvdGltZ18yMDE4MDcwOTIyMDg0MF8xNDYuanBn.jpg)

留意我标记的位置，出现(pyweb)，说明是成功进入虚拟环境。

# CentOS7安装MySQL（完整版）

### 1 下载并安装MySQL官方的 Yum Repository

```
wget -i -c http://dev.mysql.com/get/mysql57-community-release-el7-10.noarch.rpm
```

**2, 使用上面的命令就直接下载了安装用的Yum Repository，大概25KB的样子，然后就可以直接yum安装了。**

```
 yum -y install mysql57-community-release-el7-10.noarch.rpm
```

**3,安装MySQL服务器**

```
yum -y install mysql-community-server
```

### 4, MySQL数据库设置

首先启动MySQL

```
systemctl start  mysqld.service
```

查看MySQL运行状态，运行状态如图：

```
systemctl status mysqld.service
```

![img](https://bbsmax.ikafan.com/static/L3Byb3h5L2h0dHBzL2ltZy1ibG9nLmNzZG4ubmV0LzIwMTgwNTMxMTY0NTUzMjQ2.jpg)

此时MySQL已经开始正常运行，不过要想进入MySQL还得先找出此时root用户的密码，通过如下命令可以在日志文件中找出密码：

```
grep "password" /var/log/mysqld.log
```

![img](https://bbsmax.ikafan.com/static/L3Byb3h5L2h0dHBzL2ltZzIwMTguY25ibG9ncy5jb20vYmxvZy8xNTE0NDM4LzIwMTkwMy8xNTE0NDM4LTIwMTkwMzMxMTE1NjQzODQ3LTMwODg1MTEzNC5wbmc=.jpg)

如下命令进入数据库：

```
 mysql -uroot -p
```

输入初始密码（是上面图片最后面的 no;e!5>>alfg），此时不能做任何事情，因为MySQL默认必须修改密码之后才能操作数据库：

```
ALTER USER 'root'@'localhost' IDENTIFIED BY 'new password';
```

其中‘new password’替换成你要设置的密码，注意:密码设置必须要大小写字母数字和特殊符号（,/';:等）,不然不能配置成功

**redis安装**

## 下载安装包

```
wget http://download.redis.io/releases/redis-4.0.2.tar.gz
```

## 解压安装包并安装

```
tar xzf redis-4.0.2.tar.gzcd redis-4.0.2makemake install
```

**5, 开启mysql的远程访问**

执行以下命令开启远程访问限制（注意：下面命令开启的IP是 192.168.0.1，如要开启所有的，用%代替IP）：

```
grant all privileges on *.* to 'root'@'192.168.0.1' identified by 'password' with grant option;
```

然后再输入下面两行命令

```
mysql> flush privileges;mysql> exit
```

然后在windows远程测试（也可以用可视化第三方navcat等）

```
mysql -h 148.70.75.219 -P 3306 -uroot -p
```

**最后开始进行项目配置**

1，安装uwsgi

```
pip install uwsgi
```

2,配置uwsgi，在项目根目录下(manage.py同层目录)，新建uwsgi.ini

```
# mysite_uwsgi.ini file [uwsgi] # Django-related settings# 项目根目录的路径chdir           = /data/wwwroot/wtp_02# Django's wsgi file#wsgi-file       = demo01/wsgi.pymodule = wtp_02.wsgi:application# uwsgi服务器的角色master          = true# 进程7数processes=4# 线程数threads=2# 存放进程编号的文件pidfile= uwsgi.pid# the socket (use the full path to be safesocket=:8082#http          = 127.0.0.1:8002 #stats = 127.0.0.1:9191# ... with appropriate permissions - may be needed#chmod-socket    = 664# clear environment on exitvacuum          = true# 日志文件，因为uwsgi可以脱离终端在后台运行，日志看不见。我们以前的runserver是依赖终端的daemonize=uwsgi.log # 虚拟环境的目录pythonpath = /data/env/pyweb
```

然后启动uwsgi

```
uwsgi -i uwsgi.ini
```

我们查下是否启动成功

![img](https://bbsmax.ikafan.com/static/L3Byb3h5L2h0dHBzL2ltZzIwMTguY25ibG9ncy5jb20vYmxvZy8xNTE0NDM4LzIwMTkwMy8xNTE0NDM4LTIwMTkwMzMxMTMyMTUzOTMwLTc1MjYxMDA0MC5wbmc=.jpg)

通过查询日志，成功运行，证明配置没有问题

**配置项目静态文件**

修改你的dango setting.py

```
DEBUG = False ALLOWED_HOSTS = ['*'] STATIC_ROOT = os.path.join(BASE_DIR,'static')  #静态文件收集 配置路径且将 STATICFILES_DIRS  注释
```

然后收集你项目里的所有静态文件，

```
python manage.py collectstatic
```

**3，安装nginx**

切进 该 /usr/local/  目录下，执行下面命令

```
wget http://nginx.org/download/nginx-1.13.7.tar.gz
```

下载完成后，执行解压命令：

```
tar -zxvf nginx-1.13.7.tar.gz
```

进入解压后的nginx-1.13.7文件夹，依次执行以下命令：

```
./configuremakemake install
```

nginx一般默认安装好的路径为/usr/local/nginx
在/usr/local/nginx/conf/中先备份一下nginx.conf文件，以防意外。

```
cp nginx.conf nginx.conf.bak
```

然后打开nginx.conf，把原来的内容删除，直接加入以下内容：

```
user  root;worker_processes  1;###error_log  logs/error.log;##error_log  logs/error.log  notice;##error_log  logs/error.log  info;###pid        logs/nginx.pid;##events {    worker_connections  1024;}##    http {        include       mime.types;        default_type  application/octet-stream;        sendfile        on;        keepalive_timeout  65;        upstream django {        #django项目的端口号 和uwsgi里面的端口号保存一致            server 127.0.0.1:8082; # for a web port socket (we'll use this first)        }# configuration of the serverserver {# nginx服务的端口号 不用修改listen 8001;# the domain name it will serve for# 这里可以填写你的ip地址或者域名server_name 148.70.75.219;charset     utf-8;  gzip on;  gzip_disable "msie6";  gzip_proxied any;  gzip_min_length 1k;  gzip_comp_level 4;  gzip_types text/plain text/css application/json application/x-javascript text/javascript text/xml image/jpeg image/png image/gif; # max upload sizeclient_max_body_size 75M;   # adjust to taste # Django media#location /media  {#    alias /home/x/work/liebiao/lbpro/media;  # 指向django的media目录#} location /static {    alias /home/x/work/liebiao/lbpro/static; # 指向django的static目录} # Finally, send all non-media requests to the Django server.location / {    uwsgi_pass  django;    include     uwsgi_params; # the uwsgi_params file you installed}}}
```

配置完成后，启动nginx

```
/usr/local/nginx/sbin/nginx -c /usr/local/nginx/conf/nginx.conf    #启动nginx./nginx -s reload    　　　　　　　　　　　　　　　　　　　　　　　　　　　 #重启nginx     (/usr/local/nginx/sbin下)./nginx       　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　#启动nginx
```

查询是否启动成功

![img](https://bbsmax.ikafan.com/static/L3Byb3h5L2h0dHBzL2ltZzIwMTguY25ibG9ncy5jb20vYmxvZy8xNTE0NDM4LzIwMTkwMy8xNTE0NDM4LTIwMTkwMzMxMTQwMzU0ODI2LTE4NzQyNTI0MDYucG5n.jpg)

```
lsof -i:8000         查端口占用ps -ef|grep nginx    查端口号pkill -9 nginx       强制停止运行
```

现在打开浏览器即可进行访问。