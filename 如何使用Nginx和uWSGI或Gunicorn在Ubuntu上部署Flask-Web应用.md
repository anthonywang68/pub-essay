---
title: 如何使用Nginx和uWSGI或Gunicorn在Ubuntu上部署Flask Web应用
date: 2019-09-23 08:22:27
tags:
---

`WSGI`（Web Server Gateway Interface），翻译为`Python web`服务器网关接口，即`Python`的`Web`应用程序（如`Flask`）和`Web`服务器(如`Nginx`)之间的一种通信协议。也就是说，如果让你的`Web`应用在任何服务器上运行，就必须遵循这个协议。

那么实现`WSGI`协议的web服务器有哪些呢？就比如`uWSGI`与`gunicorn`。

<!--more-->

两者都可以作为Web服务器。可能你在许多地方看到的都是采用`Nginx` + `uWSGI`（或`gunicorn`）的部署方式。实际上，直接通过`uWSGI`或`gunicorn`直接部署也是可以让外网访问的，那你可能会说，那要`Nginx`何用？别急，那么接来下介绍另一个Web服务器——`Nginx`

`Nginx`作为一个高性能Web服务器，具有负载均衡、拦截静态请求、高并发...等等许多功能，你可能要问了，这些功能和使用`Nginx` + `WSGI`容器的部署方式有什么关系？

首先是负载均衡，如果你了解过[OSI模型](https://baike.baidu.com/item/OSI模型)的话，其实负载均衡器就是该模型中4~7层交换机中的一种，它的作用是能够仅通过一个前端唯一的URL访问分发到后台的多个服务器，这对于并发量非常大的企业级Web站点非常有效。在实际应用中我们通常会让`Nginx`监听（绑定）`80`端口，通过多域名或者多个location分发到不同的后端应用。

其次是拦截静态请求，简单来说，`Nginx`会拦截到静态请求（静态文件，如图片），并交给自己处理。而动态请求内容将会通过`WSGI`容器交给`Web`应用处理;

`Nginx`还有其他很多的功能，这里便不一一介绍。那么前面说了，直接通过`uWSGI`或`gunicorn`也可以让外网访问到的，但是鉴于`Nginx`具有高性能、高并发、静态文件缓存、及以上两点、甚至还可以做到限流与访问控制，所以选择`Nginx`是很有必要的；

这里可以说明，如果你选择的架构是：Nginx + WSGI容器 + web应用，WSGI容器相当于一个中间件；如果选择的架构是uWSGI + web应用，WSGI容器则为一个web服务器

# 实际部署：

> 该篇部署的教程是在你已经购买好虚拟主机，并且已经搭建好开发环境的前提下进行的，如果你还没有搭建好开发环境，可以参考我写的文档：
>
> [阿里云Ubuntu云服务器上搭建Python和Flask的开发环境](https://www.showdoc.cc/page/46561890643919)

普遍的部署方式都是通过让`Nginx`绑定`80`端口，并接受客户端的请求将动态内容的请求反向代理给运行在本地端口的`uWSGI`或者`Gunicorn`，所以既可以通过`Nginx` + `uWSGI`也可以通过`Nginx` + `Gunicorn`来部署`Flask`应用，这篇教程中都将一一介绍这两种方法

当然采用不同的`WSGI`容器，`Nginx`中的配置也会有所不同

## 1. Nginx + uWSGI：

### 1.1 配置uWSGI：

我们现在虚拟环境下安装好`uWSGI`：

```ruby
(venv) $ pip install uwsgi
```

安装完成之后我们在项目的目录下（即你实际创建的Flask项目目录，在本文所指的项目目录都假设为/www/demo）创建以`.ini`为扩展名的配置文件。在设置与`Nginx`交互的时候有两种方式：
 第一种是通过配置网络地址，第二种是通过本地的`.socket`文件进行通信。需要注意的是，不同的交互方式下，`Nginx`中的配置也会有所不同

如果采用的是第一种网络地址的方式，则将之前创建`uwsgi.ini`配置文件添加如下的配置内容：

```csharp
[uwsgi]
socket = 127.0.0.1:8001  //与nginx通信的端口
chdir = /www/demo/     //你的Flask项目目录
wsgi-file = run.py 
callable = app      //run.py文件中flask实例化的对象名
processes = 4     //处理器个数
threads = 2     //线程个数
stats = 127.0.0.1:9191   //获取uwsgi统计信息的服务地址
```

这里的`wsgi-file`参数所指的`run.py`其实是启动文件，你也可以使用`manage.py`。不过我通常习惯创建一个这样的文件，可以直接运行该文件来启动项目：

```jsx
from app import app
if __name__ == '__main__':
    app.run()
```

保存好配置文件后，就可以通过如下的命令来启动应用了：

```ruby
(venv) $ uwsgi uwsig.ini
```

如果你采用的是第二种本地`socket`文件的方式，则添加如下的配置内容：

```jsx
[uwsgi]
socket = /www/demo/socket/nginx_uwsgi.socket  //与nginx通信的socket文件
chdir = /www/demo/  
wsgi-file = run.py
callable = app  
processes = 4  
threads = 2 
stats = 127.0.0.1:9191
```

可以看到，其实与网络地址的配置方式只有`socket`参数的配置不同，在这里填写好路径名和文件名并启动`uWSGI`后，将会自动在改目录下生成`nginx_uwsgi.socket`文件，这个文件就是用来与`Nginx`交互的。

### 1.2 配置Nginx

首先我们来通过`apt`安装`Nginx`：

```csharp
$ sudo apt-get install nginx
```

安装完成之后，我们`cd`到`/etc/nginx/`的目录下（可能由于不同系统导致不同的Nginx发行版缘故，目录有所差别，在此只针对`Ubuntu`中的发行版的Nginx），可以看到`Nginx`的所有配置文件。

其中`nginx.conf`文件为主配置文件，可以用来修改其全局配置；`sites-available`存放你的配置文件，但是在这里添加配置是不会应用到`Nginx`的配置当中，需要软连接到同目录下的`sites-enabled`当中。但是在我实际操作的过程中中，当我在`sites-available`修改好配置文件后，会自动更新到`sites-enabled`。如果没有的话，则需要像上述的操作那样，将修改好的配置文件[软链接](https://www.cnblogs.com/kex1n/p/5193826.html)到`sites-enabled`当中

在上边说到，配置`uWSGI`有两种与`Nginx`交互的方式，那么选择不同的方式的话在`Nginx`的配置也会有所不同：

**第一种：网络配置方式**：

这里的`proxy_set_header`设置的三个参数的作用都是能够直接获得到客户端的`IP`，如果你感兴趣可以参考：[Nginx中proxy_set_header 理解](https://www.jianshu.com/p/cc5167032525)

用`include uwsgi_params`导入`uWSGI`所引用的参数，通过`uwsgi_pass`反向代理给在`localhost:8001`运行的`uWSGI`：

```php
server {
        listen 80; # 监听的端口号
        root /www/demo; #Flask的项目目录
        server_name xxx.xx.xxx.xxx; # 你的公网ip或者域名
        location / {
            proxy_set_header x-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header Host $http_host;
            include uwsgi_params;
            uwsgi_pass localhost:8001;
        }
        # 配置static的静态文件：
        location ~ ^\/static\/.*$ {
            root /www/demo; # 注意！这里不需要再加/static了
        }
}
```

在每次完Nginx配置文件内容后，需要通过如下的命令来重启Nginx：

```ruby
$ nginx -s reload
```

**第二种：socket文件方式**：

与上边的配置内容大体相同，只是在配置`uwsgi_pass`不是反向代理给网络地址，而是通过`socket`文件进行交互，我们只需要指定之前设置的路径和文件名即可：

```php
server {
        listen 80;
        root /www/demo;
        server_name xxx.xx.xxx.xxx;
        location / {
            proxy_set_header x-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header Host $http_host;
            include uwsgi_params;
            uwsgi_pass unix:/www/demo/socket/nginx_uwsgi.socket;
        }
        location ~ ^\/static\/.*$ {
            root /www/demo;
        }
}
```

## 2. Nginx + Gunicorn

### 2.1 配置Gunicorn:

首先先在虚拟环境下安装`Gunicorn`：

```ruby
(venv) $ pip install gunicorn
```

安装完成后，我们来创建以`.py`结尾的配置文件，这里我参考了Jiyuankai的[GitHub](https://github.com/jiyuankai/myblog/blob/master/gunicorn_config.py)关于`Gunicorn`的配置文件内容：

```jsx
from gevent import monkey
monkey.patch_all()
import multiprocessing
debug = True
loglevel = 'debug'
bind = '127.0.0.1:5000' //绑定与Nginx通信的端口
pidfile = 'log/gunicorn.pid'
logfile = 'log/debug.log'
workers = multiprocessing.cpu_count() * 2 + 1
worker_class = 'gevent' //默认为阻塞模式，最好选择gevent模式
```

需要注意的是要在配置文件的同层目录下创建`log`文件，否则运行`gunicorn`将报错。添加完配置内容并保存为`gconfig.py`文件后，我们就也可以通过`gunicorn`来运行`Flask`应用了:

```ruby
(venv)$ gunicorn -c /www/demo/gconfig.py run:app
```

### 2.2 配置Nginx:

和`uWSGI`的任意一种配置方法类似，只是在`location`中的配置有所不同：

```php
server {
        listen 80; 
        root /www/demo;
        server_name xxx.xx.xxx.xxx;
        location / {
            proxy_set_header x-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header Host $http_host;
            proxy_pass http://localhost:5000/; # gunicorn绑定的端口号
        }
        # 配置static的静态文件：
        location ~ ^\/static\/.*$ {
            root /www/demo;
        }
}
```

通过Gunicorn的Nginx配置中，我们只需要通过`proxy_pass`参数反向代理给运行在`http://localhost:5000/`上的Gunicorn

# 三、守护进程

如果你采取如上的任意一种部署方式，在Nginx与uWSGI或Gunicorn同时运行，并且配置无误的状态下，那么你现在应该是可以通过你的公网`ip`或者域名访问到你的网站了。

但是还有一个问题，到目前为止，uWSGI和gunicorn都是直接通过命令行运行，并不能够在后台运行，也是当我们关闭了xShell（或者你使用的是Putty及其他SSH连接的软件），将无法再访问到你的应用。所以我们需要让uWSGI或gunicorn在后台运行，也就是所谓的daemon（守护进程）。

### 1. nohup：

如果你熟悉Linux命令，你应该知道在Linux中后台运行可以通过`nohup`命令，例如我们要让gunicorn在后台运行，我们只需要运行`nohup`命令：

```ruby
(venv) $ nohup gunicorn -c gconfig.py run:app &
```

运行后你可以通过`ps -e | grep gunicorn`指令来查看到当前gunicorn的运行状态：



![img](https://upload-images.jianshu.io/upload_images/11576306-0fcd5341a938f707.png?imageMogr2/auto-orient/strip|imageView2/2/w/568)

image

如果你选择的是uWSGI，同样也可以通过`nohup`命令来实现守护进程：

```ruby
(venv) $ nohup uwsgi uwsgi.ini &
```

这样你就可以关闭连接服务器的终端，还能通过你的浏览器访问到你的`Flask`应用了！

### 2. supervisor

但是`nohup`运行的后台程序并不能够可靠的在后台运行，我们最好让我们的后台程序能够监控进程状态，还能在意外结束时自动重启，这就可以使用一个使用Python开发的进程管理程序supervisor。
 参考：https://www.cnblogs.com/Dicky-Zhang/p/6171954.html

首先我们通过`apt`来安装supervisor：

```csharp
$ sudo apt-get install supervisor
```

安装完成后，我们在`/etc/supervisor/conf.d/`目录下创建我们控制进程的配置文件，并以.conf结尾，这样将会自动应用到主配置文件当中，创建后添加如下的配置内容：

```jsx
[program:demo]
command=/www/demo/venv/bin/gunicorn -c /pushy/blog/gconfig.py run:app
directory=/www/demo  //项目目录
user=root
autorestart=true //设置自动重启
startretires=3  //重启失败3次
```

在上面的配置文件中，`[program:demo]`设置了进程名，这与之后操作进程的状态名称有关，为`demo`;`command`为进程运行的命令，必须使用绝对路径，并且使用虚拟环境下的`gunicorn`命令；`user`指定了运行进程的用户，这里设置为`root`

保存配置文件之后，我们需要通过命令来更新配置文件：

```ruby
$ supervisorctl update
```

命令行将显示：`demo: added process group`，然后我们来启动这个`demo`进程：

```ruby
$ supervisorctl start demo
```

当然你也直接在命令行输入`supervisorctl`进入supevisor的客户端，查看到当前的进程状态：

```css
demo        RUNNING   pid 17278, uptime 0:08:51
```

通过`stop`命令便可以方便的停止该进程：

```undefined
supervisor> stop demo
```