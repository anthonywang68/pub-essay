---
title: django-debug-toolbar
date: 2019-09-28 18:05:03
tags:
---



django-debug-toolbar 是一组可配置的面板，可显示有关当前请求/响应的各种调试信息，并在单击时显示有关面板内容的更多详细信息。

<!--more-->

[github地址](https://github.com/jazzband/django-debug-toolbar)

[文档地址](http://django-debug-toolbar.readthedocs.io/en/stable/)

### 安装

```
pip3 install django-debug-toolbar
```

### 配置

\1. settings.py中

将 debug_toolbar 添加到 INSTALL_APPS 中

```
INSTALLED_APPS = [
    …
    'debug_toolbar',
]
```

\2. urls.py中

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
from django.conf import settings
from django.conf.urls import include, url

if settings.DEBUG:
    import debug_toolbar
    urlpatterns = [
        url(r'^__debug__/', include(debug_toolbar.urls)),
    ] + urlpatterns
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

\3. settings.py中

在中间件中加入DebugToolbarMiddleware

```
MIDDLEWARE = [
    # ...
    'debug_toolbar.middleware.DebugToolbarMiddleware',
    # ...
]
```

\4. 如果是本机调试，还在将127.0.0.1加入 INTERNAL_IPS

在settings.py中加入以下配置项：

```
INTERNAL_IPS = [‘127.0.0.1’, ]
```

\5.  配置jQuery的URL

django-debug-toolbar 默认使用的是Google的地址，默认配置如下：

```
JQUERY_URL = '//ajax.googleapis.com/ajax/libs/jquery/2.2.4/jquery.min.js'
```

国内用不了的话可以在settings.py中配置一下，例如（我这里特意选用了和原作者相同版本的jQuery）：

```
DEBUG_TOOLBAR_CONFIG = {
    "JQUERY_URL": '//cdn.bootcss.com/jquery/2.2.4/jquery.min.js',
}
```

或者你如果在Django项目中使用了jquery的话就可以直接将这一项置为空，那么django-debug-toolbar 就会使用你项目中用到的jquery:

```
DEBUG_TOOLBAR_CONFIG = {
    "JQUERY_URL": '',
}
```

 

### 使用

访问具体路径的时候在页面右侧有各项配置面板，点击即可查看各种调试信息。

![img](https://images2018.cnblogs.com/blog/867021/201806/867021-20180629213348582-1390563365.png)