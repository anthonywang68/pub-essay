---
title: 外部脚本调用django
date: 2019-09-17 22:14:13
tags:
---

#  Python外部脚本调用Django项目Model表

在实际生产中有时候会出现这种情况，原本运行了一个Django项目，后面又需要一些外部脚本进行辅助，

<!--more-->

而这些脚本又不希望集成到项目当中，但是又需要用到Django项目的Model，这时候是无法像在项目当中那样直接使用的，需要我们先做些处理。

这里假设项目所在路径为`/home/icebug/workspace/my_blog`

在脚本头部添加如下内容：

```
import os
import sys

import django

sys.path.append('/home/icebug/workspace/my_blog')  # 将项目路径添加到系统搜寻路径当中
os.environ['DJANGO_SETTINGS_MODULE'] = 'my_blog.setting'  # 设置项目的配置文件

django.setup()  # 加载项目配置


# 开始实现功能模块
```

#  Django框架之在Python脚本中调用Django环境

在项目中我们使用的外部脚本需要调用Django模型层的类，此时必须要先在自己的脚本中为`os.environ`设置一个键值对，这个键值对要跟你的项目的根目录中`manage.py`文件里面的内容相同! 外部脚本的代码如下：

```
# -*- coding:utf-8 -*-
import os

if __name__ == '__main__':
    os.environ.setdefault("DJANGO_SETTINGS_MODULE", "MultiTables.settings")
    import django
    django.setup()
    # 注意引入要在上面三句之后
    from book import models
    ret = models.Book.objects.all()
    print(ret)
```

然后，我们看看目录结构： [![img](https://camo.githubusercontent.com/261d7971ea9e33ced7a69b8ef1828c6935ced1f8/687474703a2f2f7768772e707974686f6e61762e636e2f2545352541342539362545392538332541382545372538452541462545352541322538333131312e706e67)](https://camo.githubusercontent.com/261d7971ea9e33ced7a69b8ef1828c6935ced1f8/687474703a2f2f7768772e707974686f6e61762e636e2f2545352541342539362545392538332541382545372538452541462545352541322538333131312e706e67) 其实，Django项目在启动之初会加载用户自定义的配置文件，在`os.environ`这个字典中去设置一个key为`DJANGO_SETTINGS_MODULE`，value为`MultiTables.settings`（MultiTables是项目的名字）的键值对。 然后利用`importlib模块`与`反射`的方法找到用户配置文件中的配置再进行接下来的操作。 上面代码中的前三行表示`我们在外部脚本中调用了Django环境`，接下来就可以操作项目中的Model了。