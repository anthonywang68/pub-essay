---
title: alembic用法
date: 2019-11-25 21:53:04
tags:
---

alembic使用方式跟git有点类似，表现在两个方面，第一个，alemibi的所有命令都是以alembic开头；

<!--more-->

第二，alembic的迁移文件也是通过版本进行控制的。安装方式：**pip install alembic**

alembic重要作用是用来做ORM模型与数据库的迁移与映射。

### **1.1.alembic的使用**

 （1）定义模型

models.py

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
from sqlalchemy import Column,Integer,String,create_engine
from  sqlalchemy.ext.declarative import declarative_base

DB_URI = "mysql+pymysql://root:123456@127.0.0.1:3306/alembic_demo?charset=utf8"

engine = create_engine(DB_URI)

Base = declarative_base(engine)

class User(Base):
    __tablename__ = 'user'
    id = Column(Integer,primary_key=True,autoincrement=True)
    username = Column(String(50),nullable=False)
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

（2）在cmd终端初始化，创建一个仓库

```
alembic init learn_alembic
```

（3）修改配置文件，指定连接的数据库

alembic.ini

```
sqlalchemy.url = mysql+pymysql://root:123456@127.0.0.1:3306/alembic_demo?charset=utf8
```

（4）将models所在的目录路径添加到env.py，并指定target_metadata

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
import sys,os
# 1.__file__：当前文件（env.py）
#2.os.path.dirname(__file__)：获取当前文件的目录
#3.os.path.dirname(os.path.dirname(__file__))：获取当前文件目录的上一级目录
#4.sys.path: python寻找导入的包的所有路径
sys.path.append(os.path.dirname(os.path.dirname(__file__)))
import models


target_metadata = models.Base.metadata
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

（5）生成迁移脚本

```
alembic revision --autogenerate -m "第一次提交"
```

（6）将生成的迁移脚本映射到数据库中

```
alembic upgrade head
```

以后如果想要添加或修改模型，重复5,6步骤即可

 

### **1.2.alembic常用命令和经典错误解决办法**

**常用命令和参数解释：**

- init:创建一个alembic仓库
- rebision:创建一个新的版本文件
- --autogenerate:自动将当前模型的修改，生成迁移脚本
- -m:本次迁移做了哪些修改
- upgrade:将指定版本的迁移文件映射到数据库中，会执行版本文件中的upgrade函数
- head:代表当前的迁移脚本的版本号
- downgrade:会执行指定版本的迁移文件中的downgrade函数
- heads:展示当前可用的heads脚本文件
- history:列出所有的迁移版本及其信息
- current:展示当前数据库中的版本号

 **经典错误**

1.FAILED:Target databases is not up to date.

  原因：主要是heads和current不相同。current落后于heads的版本

  解决办法：将current移动到head上。alembic upgrade head

2.FAILED:Can't locate revision identified by 'xxxxxxx'

  原因：数据库中存的版本号不在迁移脚本文件中

  解决办法：删除数据的alembic_version表中的数据，重新执行alembic upgrade head

 

### **1.3.current命令使用介绍**

用alembic工具：数据库中会自动生成一张表alembic_version

![img](https://images2018.cnblogs.com/blog/1299879/201805/1299879-20180527141931870-1336084115.png)

在数据库中可以查看当前的版本号

![img](https://images2018.cnblogs.com/blog/1299879/201805/1299879-20180527142145134-251564843.png)

在cmd终端也可以通过current命令查看

```
alembic current
```

 

### **1.4.Flask-SQLAlchemy下使用alembic**

（1）config.py

```
DB_URI = "mysql+pymysql://root:123456@127.0.0.1:3306/flask_alembic_demo?charset=utf8"

SQLALCHEMY_DATABASE_URI = DB_URI
```

（2）flask_alembic_demo.py

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
from flask import Flask
from flask_sqlalchemy import SQLAlchemy
import config

app = Flask(__name__)
app.config.from_object(config)

db = SQLAlchemy(app)

class User(db.Model):
    __tablename__ = 'user'
    id = db.Column(db.Integer,primary_key=True,autoincrement=True)
    username = db.Column(db.String(50),nullable=False)
  

@app.route('/')
def hello_world():
    return 'Hello World!'

if __name__ == '__main__':
    app.run()
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

（3）初始化

```
alembic init alembic
```

（4）alembic.ini

```
sqlalchemy.url = mysql+pymysql://root:123456@127.0.0.1:3306/flask_alembic_demo?charset=utf8
```

（5）env.py

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
import sys,os
sys.path.append(os.path.dirname(os.path.dirname(__file__)))
import flask_alembic_demo

#用的是db.Model
target_metadata = flask_alembic_demo.db.Model.metadata
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

（6）生成迁移脚本

```
alembic revision --autogenerate -m "first commit"
```

（7）upgrade到数据库

```
alembic upgrade head
```

（8）添加字段

假入想添加一个字段age

```
class User(db.Model):
    __tablename__ = 'user'
    id = db.Column(db.Integer,primary_key=True,autoincrement=True)
    username = db.Column(db.String(50),nullable=False)
    age = db.Column(db.Integer)
```

重复步骤即可

```
alembic revision --autogenerate -m "add column age"

alembic upgrade head
```

 