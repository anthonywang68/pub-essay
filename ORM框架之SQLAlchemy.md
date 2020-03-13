---
title: ORM框架之SQLAlchemy
date: 2019-09-19 17:39:05
tags:
---

Django的ORM虽然强大，但是毕竟局限在Django，而SQLAlchemy是Python中的ORM框架；

以下是整理的关于SQLAlchemy的笔记：

#### SQLAlchemy的作用是：类/对象--->SQL语句--->通过pymysql/MySQLdb模块--->提交到数据库执行；

<!--more-->

创建数据库单表

```python
#创建单表
#!/usr/bin/env python
# -*- coding:utf-8 -*-
import datetime
from sqlalchemy import create_engine
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy import Column, Integer, String, Text, ForeignKey, DateTime, UniqueConstraint, Index

Base = declarative_base()


class Users(Base):
    __tablename__ = 'users'                #表名称
    id = Column(Integer, primary_key=True) # primary_key=True设置主键
    name = Column(String(32), index=True, nullable=False) #index=True创建索引， nullable=False不为空。

def init_db(): #根据类创建数据库表
    engine = create_engine(
        "mysql+pymysql://webproject:web@192.168.1.18:3306/web?charset=utf8",
        max_overflow=0,   # 超过连接池大小外最多创建的连接
        pool_size=5,      # 连接池大小
        pool_timeout=30,  # 池中没有线程最多等待的时间，否则报错
        pool_recycle=-1   # 多久之后对线程池中的线程进行一次连接的回收（重置）
    )

    Base.metadata.create_all(engine) #这行代码很关键哦！！ 读取继承了Base类的所有表在数据库中进行创建

if __name__ == '__main__':
    init_db()                           #执行创建
```

添加一条记录

```python
from SqlALchemy.models import Users
from sqlalchemy.orm import sessionmaker
from sqlalchemy import create_engine

engine = create_engine( "mysql+pymysql://webproject:web@192.168.1.18:3306/web?charset=utf8")
Session = sessionmaker(bind=engine)

# 每次执行数据库操作时，都需要创建一个会话
session = Session()

# ############# 执行ORM操作 #############
obj1 = Users(name="张根") #创建Users对象=1行数据
session.add(obj1)          #添加到表中

# 提交事务
session.commit()
# 关闭session
session.close()
```

```python
    # 一、SQLAchemy ORM框架
    # SQLAlchemy是Python编程语言下的一款ORM框架，该框架建立在数据库API之上，使用关系对象映射进行数据库操作，
    # 简言之便是：将对象转换成SQL，然后使用数据API执行SQL并获取执行结果
    # 换言值通过pymsql需要写原生sql语句（自己需要拼接sql语句不太有重用性，不方便扩展；你不是dba很多情况写不很优化的sql语句），这个SQLAchemy解决这个问题
    # sqlachemy就是直接封装原生sql语句
    # orm的优点：
    #     隐藏了数据访问细节，“封闭”的通用数据库交互，ORM的核心。他使得我们的通用数据库交互变得简单易行，并且完全不用考虑该死的SQL语句。快速开发，由此而来。
    #     ORM使我们构造固化数据结构变得简单易行。
    # 缺点：
    #     无可避免的，自动化意味着映射和关联管理，代价是牺牲性能（早期，这是所有不喜欢ORM人的共同点）。
    #     现在的各种ORM框架都在尝试使用各种方法来减轻这块（LazyLoad，Cache），效果还是很显著的。
    # 支持多种数据库连接
    # MySQL-Python
    #     mysql+mysqldb://<user>:<password>@<host>[:<port>]/<dbname>
    # pymysql
    #     mysql+pymysql://<username>:<password>@<host>/<dbname>[?<options>]
    # MySQL-Connector
    #     mysql+mysqlconnector://<user>:<password>@<host>[:<port>]/<dbname>
    # cx_Oracle
    #     oracle+cx_oracle://user:pass@host:port/dbname[?key=value&key=value...]
    # 二、使用群体
    # 在Python中，最有名的ORM框架是SQLAlchemy。用户包括openstack＼Dropbox等知名公司或应用
    # pip install SQLAlchemy    # install SQLAlchemy-1.2.18
    # pip install pymysql       # 使用pymysql或者mysqldb-python(目前不支持python3)，使用这个软件连接手机看
    print("----- 创建表结构 ---------")
    # 三、创建表结构
    import sqlalchemy
    from sqlalchemy import create_engine
    from sqlalchemy.ext.declarative import declarative_base
    from sqlalchemy import Column, Integer,String
    from sqlalchemy.orm import sessionmaker
    # create_engine创建连接数据库
    #engine=create_engine("mysql+pymysql://wugq:wugq@192.168.2.254/wugq_std",
    #              encoding="utf-8", echo=True)
    # 调试sql非常有用
    engine=create_engine("mysql+pymysql://wugq:wugq@192.168.2.254/wugq_std",encoding="utf-8") #没有echo=True就不会显示详细执行信息
    # 生成orm基类
    Base = declarative_base()
    # 需要继承上面的ORM基类
    class User(Base):
        __tablename__ = "user"    # 表名
        id = Column(Integer, primary_key=True)
        name = Column(String(32))
        password = Column(String(64))
        # 增加格式输出，可以返回data里面的内容，返回格式是[<1 name:alex>]
        def __repr__(self):
            return "<%s name:%s passwd:%s>" % (self.id,self.name,self.password)
    class msg(Base):
        __tablename__ = "msg"    # 表名
        id = Column(Integer, primary_key=True)
        age = Column(String(32))
        sex = Column(String(64))
        # 增加格式输出，可以返回data里面的内容，返回格式是[<1 name:alex>]
        def __repr__(self):
            return "<%s name:%s sex:%s>" % (self.id,self.age,self.sex)
    # 创建表结构，直接调用Base基类的方法，我们以前都是学习的儿子继承调用父亲；
    # 这里通过父亲base调用儿子engine，重写engine方法，儿子上面的东西都继承了
    Base.metadata.create_all(engine)
    # 创建类，以括号的形式实例化
    Session_class = sessionmaker(bind=engine)   #创建与数据库的会话session class，注意这里返回的是session一个class，不是实例
    Session = Session_class()                   #生成session实例，理解为mysql curosr游标
    print("----- 插入数据 ---------")
    # 四、插入数据
    # 1、创建数据
    user_obj = User(name="alex", password="alex123")   #生成你要的数据对象
    print(user_obj.name, user_obj.id)                    #此时还没有创建对象，打印id发现是None
    # 2、把数据放入session里面
    Session.add(user_obj)                # 要把创建的数据对象添加到session里，一会统一创建
    print(user_obj.name, user_obj.id)    # 此时依然没有创建
    # 3、提交
    Session.commit()        # 现在统一提交创建数据
    print("----- 查询数据 ---------")
    # 五、查询数据
    # 1、filter_by 查出是一个列表对象，一组数据
    data = Session.query(User).filter_by(name="alex").all()     #查出来是一个列表，是一条数据
    # data = Session.query(User).filter_by().all()               #查所有数据，多条数据
    # data = Session.query(User).filter_by().first()             #查询第一条数据
    # 需要手动调用
    print(data)       # 在user中增加__repr__内置方法可以直接输出内容格式[<1 name:alex>]，如果没有增加__repr__就是一个列表对象，分不清楚谁是谁
    print(data[0].name, data[0].password)  # 列表中调用方法，只能一个个调用，如果需要多个调用就需要for循环调用了，如果是用__repr__就省去了这个麻烦
    print("----- 条件查询数据 ---------")
    # 2、filter查大于
    User.id大于0的数据
    data = Session.query(User).filter(User.id>0).all()      # 大于
    data = Session.query(User).filter(User.id==1).all()     # 等于
    data = Session.query(User).filter(User.id<=2).all()     # 小于等于
    data = Session.query(User).filter(User.id>=0).all()     # 大于等于
    data = Session.query(User).filter(User.id>=0, User.name=='AA').all()        # 2个条件 AND关系多个条件
    data = Session.query(User).filter(User.id>=0).filter(User.name=='AA').all()  # 2个条件 AND关系多个条件, 结果同上面一样
    data = Session.query(User).filter().all()
    print(data)
    print("----- 修改数据 ---------")
    # 3、修改数据
    # data = Session.query(User).filter(User.id>=0).filter(User.name=='AA').first()  #获取第一条数据
    # print(data)
    # # 直接定义数据值，变成data的一个属性，直接面向对象的方式修改
    # data.name = "Jack Liu"
    # data.password = "Shit happens"
    # # 提交数据
    # Session.commit()
    print("----- 批量修改数据 ---------")
    # 4、批量修改数据
    print("----- 回滚数据 ---------")
    # 5、数据回滚
    创建一个rain的用户
    fake_user = User(name="Rain", password='123456')
    Session.add(fake_user)
    print(Session.query(User).filter(User.name.in_(['BB','Rain'])).all()) # 返回[<3 name:BB>, <4 name:Rain>]
    Session.rollback()                                                     # 事物回退
    print(Session.query(User).filter(User.name.in_(['BB','Rain'])).all()) # 返回[<3 name:BB>]， 说明<4 name:Rain>事物回滚了
    #Session.commit()
    print("----- 统计与分组 ---------")
    # 6、统计与分组
    # 统计count
    print(Session.query(User).filter(User.name.in_(['alex', 'BB'])).count())  # 统计'alex', 'BB'这2个用户总数
    # 分组
    from sqlalchemy import func   # 分组需要专门导入一个方法函数
    print(Session.query(User.name, func.count(User.name)).group_by(User.name).all()) # 返回[('alex', 1), ('BB', 1), ('CC', 2), ('Jack Liu', 1)]结果, name,count数量
    print("----- 数据删除 ---------")
    # 7、数据删除
    # 先查询出数据
    data = Session.query(User).filter(User.id>=5).filter(User.name=='CC').first()
    # 然后直接删除
    Session.delete(data)
    # 提交删除
    # Session.commit()
    print("----- 数据修改update ---------")
    # 8、数据修改
    session.query(Users).filter(Users.id > 2).update({"name" : "099"})
    session.query(Users).filter(Users.id > 2).update({Users.name: Users.name + "099"}, synchronize_session=False)
    session.query(Users).filter(Users.id > 2).update({"num": Users.num + 1}, synchronize_session="evaluate")
    session.commit()
    print("----- 多表查询 ---------")
    # 8、多表查询
    # where a.id == b.id
    print(Session.query(User, msg).filter(User.id == msg.id).all())   #
    # join
    # 需要User表和msg表之间建立了主外键关系，否则找不到报错：sqlalchemy.exc.NoForeignKeysError: Can't find any foreign key relationships between 'user' and 'msg'.
    # print(Session.query(User).join(msg).all())              # isouter=False为真使用右外连接
    # 这个也不能查询，不能使用join
    # print(Session.query(User).join(msg, isouter=True).all())  # isouter=True为真使用左外连接
    # 代码位置： http://www.cnblogs.com/wupeiqi/articles/5713330.html
    #　其他
    #　条件
    ret = session.query(Users).filter_by(name='alex').all()
    ret = session.query(Users).filter(Users.id > 1, Users.name == 'eric').all()
    ret = session.query(Users).filter(Users.id.between(1, 3), Users.name == 'eric').all()
    ret = session.query(Users).filter(Users.id.in_([1,3,4])).all()
    ret = session.query(Users).filter(~Users.id.in_([1,3,4])).all()
    ret = session.query(Users).filter(Users.id.in_(session.query(Users.id).filter_by(name='eric'))).all()
    from sqlalchemy import and_, or_
    ret = session.query(Users).filter(and_(Users.id > 3, Users.name == 'eric')).all()
    ret = session.query(Users).filter(or_(Users.id < 2, Users.name == 'eric')).all()
    ret = session.query(Users).filter(
        or_(
            Users.id < 2,
            and_(Users.name == 'eric', Users.id > 3),
            Users.extra != ""
        )).all()
    # 通配符
    ret = session.query(Users).filter(Users.name.like('e%')).all()
    ret = session.query(Users).filter(~Users.name.like('e%')).all()
    # 限制
    ret = session.query(Users)[1:2]
    # 排序
    ret = session.query(Users).order_by(Users.name.desc()).all()
    ret = session.query(Users).order_by(Users.name.desc(), Users.id.asc()).all()
    # 分组
    from sqlalchemy.sql import func
    ret = session.query(Users).group_by(Users.extra).all()
    ret = session.query(
        func.max(Users.id),
        func.sum(Users.id),
        func.min(Users.id)).group_by(Users.name).all()
    ret = session.query(
        func.max(Users.id),
        func.sum(Users.id),
        func.min(Users.id)).group_by(Users.name).having(func.min(Users.id) >2).all()
    # 连表
    ret = session.query(Users, Favor).filter(Users.id == Favor.nid).all()
    ret = session.query(Person).join(Favor).all()
    ret = session.query(Person).join(Favor, isouter=True).all()
    # 组合
    q1 = session.query(Users.name).filter(Users.id > 2)
    q2 = session.query(Favor.caption).filter(Favor.nid < 2)
    ret = q1.union(q2).all()
    q1 = session.query(Users.name).filter(Users.id > 2)
    q2 = session.query(Favor.caption).filter(Favor.nid < 2)
    ret = q1.union_all(q2).all()
    # 常用查询语法
    # Common Filter Operators
    # Here’s a rundown of some of the most common operators used in filter():
    #
    # equals:
    #
    #      query.filter(User.name == 'ed')
    # not equals:
    #
    #      query.filter(User.name != 'ed')
    # LIKE:
    #
    # query.filter(User.name.like('%ed%'))
    #
    # IN:
    #
    # NOT IN:
    # query.filter(~User.name.in_(['ed', 'wendy', 'jack']))
    #
    # IS NULL:
    #
    # IS NOT NULL:
    #
    # AND:
    # 2.1. ObjectRelationalTutorial 17
    #
    # query.filter(User.name.in_(['ed', 'wendy', 'jack']))
    # # works with query objects too:
    # query.filter(User.name.in_( session.query(User.name).filter(User.name.like('%ed%'))
    #
    # ))
    #
    # query.filter(User.name == None)
    # # alternatively, if pep8/linters are a concern
    # query.filter(User.name.is_(None))
    # query.filter(User.name != None)
    # # alternatively, if pep8/linters are a concern
    # query.filter(User.name.isnot(None))
    # SQLAlchemy Documentation, Release 1.1.0b1
    #
    # # use and_()
    #
    # from sqlalchemy import and_
    # query.filter(and_(User.name == 'ed', User.fullname == 'Ed Jones'))
    #
    # # or send multiple expressions to .filter()
    # query.filter(User.name == 'ed', User.fullname == 'Ed Jones')
    # # or chain multiple filter()/filter_by() calls
    # query.filter(User.name == 'ed').filter(User.fullname == 'Ed Jones')
    # Note: Makesureyouuseand_()andnotthePythonandoperator! • OR:
    #
    # Note: Makesureyouuseor_()andnotthePythonoroperator! • MATCH:
    #
    # query.filter(User.name.match('wendy'))
    # Note: match() uses a database-specific MATCH or CONTAINS f
```



SQLAlchemy 是 Python 中一个通过 ORM 操作数据库的框架。

SQLAlchemy对象关系映射器提供了一种方法，用于将用户定义的Python类与数据库表相关联，并将这些类（对象）的实例与其对应表中的行相关联。它包括一个透明地同步对象及其相关行之间状态的所有变化的系统，称为*工作单元*，以及根据用户定义的类及其定义的彼此之间的关系表达数据库查询的系统。

> 官方解释：
>  它提供了一整套众所周知的企业级持久性模式，旨在实现高效，高性能的数据库访问，并采用简单的Pythonic域语言。

可以让我们使用类和对象的方式操作数据库，从而从繁琐的 sql 语句中解脱出来。

ORM 就是 Object Relational Mapper 的简写，就是关系对象映射器的意思。

### 架构图



![img](https://upload-images.jianshu.io/upload_images/11414906-1b9a173bfcf6301e.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200)

千锋教育云计算

`Schema / Types`                     定义了类到表之间的映射框架(规则)

`SQL Expression Language`   封装好的 SQL 语句

`Engine`                                     操作者

`Connection Pooling`              连接池

`Dialect`                根据用户的配置，调用不同的数据库 API(Oracle, postgresql, Mysql) 并执行对应的 SQL语句

# 二、安装

```bash
shell> pip3 install sqlalchemy
```

SQLAlchemy本身无法操作数据库，其必须使用 pymsql 等第三方插件，从而实现对数据库的操作，如：

**pymysql 使用如下方式连接：**

```xml
mysql+pymysql://<username>:<password>@<host>/<dbname>[?<options>]
```

更多参考：https://docs.sqlalchemy.org/en/latest/dialects/index.html

使用 `Engine`  、`ConnectionPooling` 、`Dialect` 对数据库进行操作的流程：

`Engine` (SQLAlchemy 引擎) 使用 `ConnectionPooling` 连接数据库，之后再通过 `Dialect` 执行 SQL 语句。

# 三、连接数据库

下面一连接 `Mysql` 为例来演示连接语法的具体实现

```python
#!/usr/bin/env python
# -*- coding:utf-8 -*-
from sqlalchemy import create_engine
 
engine = create_engine(
  "mysql+pymysql://root:123@172.16.153.160:3306/dbname?charset=utf8mb4", 
  echo=True, 
  max_overflow=5)
```

echo 标志是设置SQLAlchemy日志记录的快捷方式。 启用它后，我们将看到所有生成的SQL。

Max_overflow 指定了连接池的最大连接数。

create_engine() 的返回值是一个实例引擎,它代表了一个数据库的核心接口。

此时的连接是惰性的，当create_engine()第一次返回的引擎，其实并没有试图连接到数据库之中; 只有在第一次要求它对数据库执行任务时才会发生这种情况。比如使用了以下的任一方法:

```python
engine.execute()
```

# 四、执行原生 sql 语句

```python
ret = engine.execute("select * from t1;")
# print(dir(engine))
# print(ret.fetchone())
print(ret.fetchall())
```

# 五、ORM 的基本操作

## ORM 流程

ORM 操作的流程是，一个 Engine 使用 Schema Type 创建一个特定的结构对象，之后通过 SQL Expression  Language 将该对象转换成 SQL 语句， 接着使用 ConnectionPling 连接数据库，最后通过 Dialect 执行 SQL  语句，并获取结果。

## 1. 声明一个映射类

官方解释：

使用ORM时，配置过程首先描述我们将要处理的数据库表，然后定义我们自己的类，这些类将映射到这些表。在现代SQLAlchemy中，这两个任务通常使用称为[Declarative](https://docs.sqlalchemy.org/en/latest/orm/extensions/declarative/index.html)的系统一起执行，这允许我们创建包含指令的类，以描述它们将映射到的实际数据库表。

**大白话：**

创建一个类，一个类对应了一个数据库中的一张表，类的数据属性对应了表中的字段名，这个类称为映射类。

根据映射类创建出一个一个的对象，每个对象对应了表中的一条实际的数据。



![img](https://upload-images.jianshu.io/upload_images/11414906-d60413ef1410b728.png?imageMogr2/auto-orient/strip|imageView2/2/w/889)

千锋云计算

使用Declarative系统映射的类是根据基类定义的，换句话说每个映射类需要继承这个基类。我们使用`declarative_base()` 函数可以创建这个基类，如下所示：

```python
from sqlalchemy.ext.declarative import declarative_base

Base = declarative_base()
```

现在我们有了一个`Base`，我们可以根据它定义任意数量的映射类。

我们将从一个名为 `teacher` 的表开始。

#### 示例表结构

`teacher` 表

| id   | name    | age  | city |
| ---- | ------- | ---- | ---- |
| 1    | yangge  | 18   | 北京 |
| 2    | qiangge | 19   | 北京 |
| 3    | shark   | 19   | 北京 |

类`Teacher`将是我们映射此表的类。

在类中，我们定义了有关我们将要映射的表的详细信息，主要是表名，以及列的名称和数据类型：

```python
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy import Column, Integer, String

Base = declarative_base()

class Teacher(Base):
    __tablename__ = 'teacher'
    id = Column(Integer, primary_key=True)
    name = Column(String(12))
    age = Column(String(2))
    city = Column(String(16))
    
    def __repr__(self):
        tpl = "Teacher(id={}, name={}, age={}, city={})"
        return tpl.format(self.id, self.name,
                          self.age, self.city)
```

`__repr__` 方法定义了一个对象的比较易读的显式方式

使用 Declarative 的类至少需要一个`__tablename__`属性，并且至少有一个 `Column`属于主键。

## 2. 创建表到数据库中

我们可以使用[`MetaData`](https://docs.sqlalchemy.org/en/latest/core/metadata.html#sqlalchemy.schema.MetaData) 为所有数据库中尚不存在的表向数据库发出CREATE TABLE语句。

下面，我们调用该[`MetaData.create_all()`](https://docs.sqlalchemy.org/en/latest/core/metadata.html#sqlalchemy.schema.MetaData.create_all)方法，将我们[`Engine`](https://docs.sqlalchemy.org/en/latest/core/connections.html#sqlalchemy.engine.Engine) 作为数据库连接源传递。我们将看到首先发出特殊命令以检查`teacher`表的存在，然后是实际的语句：`CREATE TABLE`
 `

```python
Base.metadata.create_all(engine)
```

执行成功后可以检查是否创建成功

**终端检查结果**

```mysql
MariaDB [(none)]> use db_1803
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
MariaDB [db_1803]> show tables;
+-------------------+
| Tables_in_db_1803 |
+-------------------+
| teacher           |
+-------------------+
1 row in set (0.00 sec)

MariaDB [db_1803]>
```

## 3. 创建映射类的实例

```python
yg_teacher = Teacher(name='yangge', 
               age='18', 
               city='BeiJing')
```

> 此时，实例对象只是在此刻环境的内存中有效，并没有在表中真正生成数据。

要想生成数据到表中，需要创建一个和数据库沟通的会话对象，利用这个会话对象对数据库中的表进行操作（增加、更新，删除、查询）

## 4. 创建会话

现在准备开始与数据库交谈，需要使用一个引擎的实例来创建一个 `Session`类的实例。

```python
from sqlalchemy.orm import sessionmaker

# 把当前的引擎绑定给这个会话
Session = sessionmaker(bind=engine) 

# 实例化
session = Session()
```

## 5. 通过 Session 实例的方法进行操作表中的数据

刚才我们已经创建了一条数据的实例了，要想把这条数据实例真正的增加到数据库中的`teacher` 表中，需要先把这条数据对象添加到会话实例中，再通过会话实例的 `commit()`  方法提交事务到数据库，此时，数据库的 `teacher` 表中才会有这条数据。

**添加单条数据到 session 中**

```python
session.add(yg_teacher)
```

此时这个数据并没有被同步的数据库中，而是处于等待的状态。

执行执行了 `commit()` 方法后，才会真正在数据表中创建数据。

如果我们查询数据库，则首先刷新所有待处理信息，然后立即发出查询。

```python
our_teacher = session.query(Teacher).filter_by(
    name='yangge').first() 
print(our_teacher)
```

> 注意：此时得到的结果是并不是 数据库表中的最终数据，而是映射类的一个对象

## 6. 提交

```python
session.commit()
```

此时，数据真正的被写入到数据库中了

**增加多条数据**

```python
session.add_all([
    Teacher(name='qiangge', age='19',city='北京'),
    Teacher(name='shark', age='19',city='北京'),
])
```

**一起提交**

```python
session.commit()
```

## 7. 关于回滚

在 commit() 之前，对实例对象的属性所做的更改，可以进行回滚。回到更改之前。

```python
session.rollback()
```

示例:

假如目前向表中添加一条新的数据，同时查询出一条已有的数据，并且把已有的数据某个字段的值进行更改。

```python
# 新增数据
session.add(Teacher(name='shark2', 
                    age='18',
                    city='ZhengZhou'))

# 修改已有数据的字段值
shark=session.query(Teacher).filter_by(name='shark').first()
print(shark.age)
shark.age = '28'
shark.name = 'shark3'

# 查询操作结果
two_user = session.query(Teacher).filter(Teacher.name.in_(['shark2','shark3'])).all()
print(two_user)

#输出的结果
[Teacher(id=4, name=shark, age=38, city='北京'),
 Teacher(id=23, name=shark2, age=18, city=ZhengZhou)]
```

此时数据的更改只是发生于会话对象的事务中，并没有发生在数据库中。

现在进行回滚操作

```python
session.rollback()
```

之后再次进行查询操作会发现已恢复到修改之前

```python
[Teacher(id=4, name=shark, age=23, city='北京')]
```

> 可以看出这里提到的回滚，本质上只是把某一条数据（也就是映射类的实例）从内存中删除而已。

# 六、ORM 进阶操作

## 1. 导入模块

```python
# 创建连接相关
from sqlalchemy import create_engine

# 和 sqlapi 交互，执行转换后的 sql 语句，用于创建基类
from sqlalchemy.ext.declarative import declarative_base

# 创建表中的字段(列)
from sqlalchemy import Column

# 表中字段的属性
from sqlalchemy import Integer, String, ForeignKey
from sqlalchemy import UniqueConstraint, Index
from sqlalchemy.orm import sessionmaker, relationship
```

## 2. 连接数据库

```python
# 创建连接对象，并使用 pymsql 引擎
conn_str = "mysql+pymysql://{user}:{pwd}@{}:3306/{db_name}?charset='utf8mb4"
connect_info = conn_str.format(user='root',
                              pwd='123456',
                              db_name='db_1803')

engine = create_engine(connect_info, max_overflow=5)
```

## 3. 创建表

```python
# 创建基类
Base = declarative_base()

# 创建单表
class Person(Base):
    __tablename__ = 'person'  # 表名
    id = Column(Integer, primary_key=True)
    # 必须指定长度，在PostgreSQL上不需要
    name = Column(String(32))
    age = Column(Integer)      # 整型

    __table_args__ = (
    # 设置联合唯一
    UniqueConstraint('id', 'name', name='uix_id_name'),
    
    # 建立索引   
    Index('uix_id_name', 'name'),
    )


def init_db():
    """创建所有定义的表到数据库中"""
    Base.metadata.create_all(engine)


def drop_db():
    """从数据库中删除所有定义的表"""
    Base.metadata.drop_all(engine)
    
# 执行创建表
#init_db()
    
# 创建会话实例对象
Session = sessionmaker(bind=engine)
session = Session()
```

## 4. 查询数据

a. 结果集内看到的是对象 、还是数据

​     `query(类名)`              返回的就是对象

​    `query(类名.字段名)`   返回的就是含有数据的元组对象

```python
# 所有数据，且结果集中是一个一个的对象
ret = session.query(Teacher).all()
# 结果 [obj1, obj2, obj3]

#  指定字段查询，返回所有的数据，是一个列表，列表内是一个一个的元组
ret = session.query(Teachers.name, Teachers.arg).all()
# 结果 [('yangge', '18'), ('qiangge', '19'), ('shark', '23')]
```

b. 迭代查询结果集

```python
for name, age, in session.query(Teacher.name, Teacher.age):
    print(name, age)
    
# 输出结果
yangge 18
qiangge 19
shark 23
```

c. 给列起别名

可以使用 `label()` 给每个列名起别名

```python
for row in session.query(Teacher.name.label('t_name')).all():
    print(row.t_name)
```

d. 条件查询

`filter_by()`   接收的是关键字参数

`filter()`         允许使用 python 的比较或关系运算符，实现更灵活的查询

```python
# filter_by()
ret = session.query(Teacher).filter_by(name='yangge').first()
# 结果 Teacher(id=2, name=yangge, age=18, city=BeiJing)

# filter()
ret = session.query(Teacher).filter(Teacher.age>'20').first()
# 结果 Teacher(id=4, name=shark, age=23, city='北京')
```

1. 关系运算符的查询

以下适用于 `filter()`

以下查询都是以这个查询对象为基础的过滤

```python
query = session.query(Teacher)
```

- 相等

```python
query.filter(Teacher.name == 'shark').all()
```

- 不相等

```python
query.filter(Teacher.name != 'shark').all()
```

- LIKE

  在某些数据库中，这个可能会不区分大小写，也有可能区分大小写。

```python
query.filter(Teacher.name.like('%sha%')).all()
```

- ILIKE

  确保忽略大小写， 大部分数据库不支持 `ilike`

```python
query.filter(Teacher.name.ilike('Sha___')).all()
```

- IN

```python
query.filter(Teacher.id.in_([2,3])).all()
```

- NOT IN

  使用波浪号`~` 表示非

```python
query.filter(~Teacher.id.in_([2,3])).all()
```

- BETWEEN

  使用 between 表示范围

```python
query.filter(Teacher.id.between(1, 3)).all()
```

- IS NULL

  > 数据库中的空字符串不是 NUll ， python 中的 None 存到数据库中是 NULL。

```python
query.filter(Teacher.name == None).all()
# 或者
query.filter(Teacher.name.is_(None)).all()
```

- IS NOT NULL

```python
query.filter(Teacher.name != None).all()
# 或者
query.filter(Teacher.name.isnot(None)).all()
```

- AND

```python
# 使用 and_()
from sqlalchemy import and_
query.filter(and_(Teacher.name == 'shark', 
                  Teacher.city == '北京')).all()

# 或者使用逗号
query.filter(Teacher.name == 'shark', 
             Teacher.city == '北京').all()
```

- OR

```python
from sqlalchemy import or_
query.filter(or_(Teacher.name == 'shark', 
                 Teacher.name == 'xiguatian')).all()
```

- AND 和 OR 的综合使用

```python
query.filter(
    or_(
        Teacher.id <= 2,
        and_(Teacher.name == 'shark', Teacher.id > 3)
    )).all()
```

e. 控制返回的查询结果集

`all()` 返回的是所有的结果集，是列表

`first()` 返回的是所有结果集中的第一个数据

```python
ret = session.query(Teacher).all()
# 结果 [obj1,obj2,obj3]

ret = session.query(Teacher).first()
# 结果 obj1
```

- one

​     提取结果集中的所有数据，假如没有或者数据多于一条则会报错

​    找到后返回的是一个元组

- one_or_none

  和 `one()` 一样，但是没找到返回 `None`

- scalar

​     `scalar()` 调用 `one()` 方法，找不到，返回 `None`

​    找到后返回的是赤裸裸的数据

- limit
- 使用 python 的切片控制输出多少行

```python
session.query(Teacher).all()[0:2]
```

-  `order by`   排序



```python
# 正序
session.query(Teacher).order_by(Teacher.name).all()

# 倒序
session.query(Teacher).order_by(Teacher.name.desc()).all()

# 先按名字排序，假如有相同的再安装 id 排序
session.query(Teacher).order_by(Teacher.name,       
                                Teacher.id.desc()).all()
```

- count 统计

```python
session.query(Teacher).filter(Teacher.age=='18').count()
```

f. 嵌套的查询

```python
# 嵌套，从最内层的查询结果中再查询想要的数据
session.query(Teacher).filter(
    Teacher.id.in_(
        session.query(Teacher.id
                      ).filter_by(
            name='yangge'))).all()
```

g. 分组统计查询

```python
from sqlalchemy.sql import func
# 统计表中所有的数据
session.query(func.count('*')).select_from(Teacher).first()

# 以年龄分组，并统计每组的数据数量
session.query(func.count(Teacher.age),Teacher.age
                    ).group_by(Teacher.age).all()

# 以年龄为分组，并统计每组的最大/最小 id 号，年龄总和/平均值，
session.query(
    func.max(Teacher.id),
    func.min(Teacher.id),
    func.sum(Teacher.age),
    func.avg(Teacher.age),
    Teacher.id
                    ).group_by(Teacher.age).all()

# 从分组的数据中再查找需要的数据
session.query(
    func.max(Teacher.id),
    func.min(Teacher.age),
    func.sum(Teacher.age),
    func.avg(Teacher.age),

    Teacher.id
    ).group_by(Teacher.age
               ).having(func.min(Teacher.id) > 2).all()
```

h. 组合

```python
# 再创建一个表
class Student(Base):
    __tablename__ = 'student'
    id = Column(Integer,primary_key=True)
    name = Column(String(12))
    age = Column(String(2))
    city = Column(String(16))

# 组合  用一条数据将两个表中的要查询的数据组合在一张表里展示出来
q1 = session.query(Teacher.name).filter(Teacher.id > 2)
q2 = session.query(Student.name).filter(Student.id < 2)
## 去重
ret = q1.union(q2).all()
## 不去重 
q1 = session.query(Teacher.name).filter(Teacher.id > 2)
q2 = session.query(Student.name).filter(Student.id < 2)
ret = q1.union_all(q2).all()
```

# 七、更新数据

```python
session.query(Teacher).filter(Teacher.id == 3).update(
    {"name" : "xiguatian"})

session.query(Teacher).filter(Teacher.id > 3).update(
    {Teacher.name: Teacher.name + "_云计算讲师"},
    synchronize_session=False) 
# 不同步，数据的更新在 commit 之后

session.query(Teacher).filter(Teacher.id > 2).update(
    {"age": int(Teacher.age + 1)}, 
    synchronize_session="evaluate") 

session.commit()
```

# 八、删除数据

```python
session.query(Teacher).filter(Teacher.id > 4).delete()
session.commit()
```

# 九、连表操作

`INNER JOIN`         内连接
 `LEFT JOIN`           左连接
 `RIGHT JOIN`         右连接
 `FULL JOIN`           完全连接

- 内连接    显示的是两个表的两列数据匹配的相关数据，并且返回的是组合后的数据
- 外连接    分为左外连接、右外连接和全连接，mysql中不支持全连接，所以以左外连接为例来讨论区别： 
  - 左外连接是以左表为主，返回的是只在join关键字前面这张表（即左表）中，所有符合 where 子句的数据，不管是否符合连接条件。即这张表的内容都要全部显示。而后面的那张表只显示匹配连接条件的数据。
  - 右外联接则刚好和左外连接相反。

## 1. 一对多

示例表：

**groups**

| id   | name  | full_name                         | cn_name      |
| ---- | ----- | --------------------------------- | ------------ |
| 1    | Other |                                   | 默认组       |
| 2    | PM    | Product Manager                   | 产品经理     |
| 3    | RD    | Research and Development engineer | 开发         |
| 4    | QA    | Qualtiy Assurance                 | 测试         |
| 5    | OP    | Operator                          | 运维         |
| 6    | DBA   | Database Administrator            | 数据库管理员 |

**users**

| id   | name      | goup_id |
| ---- | --------- | ------- |
| 1    | Yangge    | 2       |
| 2    | Tom       | 2       |
| 3    | Rose      | 3       |
| 4    | Shark     | 3       |
| 5    | Xiguatian | 5       |
| 6    | Jack      | 6       |
| 7    | new_user  | 1       |

创建表的原生语句

```mysql
CREATE TABLE groups (
    id INTEGER NOT NULL AUTO_INCREMENT, 
    name VARCHAR(5) NOT NULL, 
    full_name VARCHAR(64), 
    cn_name VARCHAR(64), 
    PRIMARY KEY (id), 
    UNIQUE (name)
)

CREATE TABLE users (
    id INTEGER NOT NULL AUTO_INCREMENT, 
    name VARCHAR(32) NOT NULL, 
    group_id INTEGER, 
    PRIMARY KEY (id), 
    FOREIGN KEY(group_id) REFERENCES groups(id)
)
```

1. 创建表

```python
from sqlalchemy import create_engine
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy import Column, Integer, String, ForeignKey
from sqlalchemy.orm import relationship

engine = create_engine("mysql+pymysql://root:123456@172.16.153.160:3306/db_1802", echo=True, max_overflow=5)
Base = declarative_base()
class Groups(Base):
    __tablename__= 'groups'
    id = Column(Integer, primary_key=True)
    name = Column(String(12), 
                  unique=True,     # 值必须唯一
                  nullable=False)  # 不允许为空
    full_name = Column(String(64), nullable=True)
    cn_name = Column(String(64))

class Users(Base):
    __tablename__= 'users'
    id = Column(Integer, primary_key=True)
    name = Column(String(32), 
                  unique=True,
                  index=True,  # 此列建立索引
                  nullable=False)
    group_id = Column(Integer, 
                      ForeignKey('groups.id'),  # 定义外键
                      default=1)  # 默认值
    
    # 下面此列与创建表无关，仅用于查询使用，group 用于正向查询，user 用于反向查询
    group = relationship('Groups',  # 字符串类型的映射类名称。 
                         backref='user')
    
# 创建所有的表结构
Base.metadata.create_all(engine)
```

1. 添加数据

```python
from sqlalchemy.orm import sessionmaker

# 把当前的引擎绑定给这个会话
Session = sessionmaker(bind=engine) 

# 实例化
session = Session()

session.add_all([
    Groups(name='Other',),
    Groups(name='PM',full_name='Product Manager', cn_name='产品经理'),
    Groups(name='RD',full_name='Research and Development engineer', cn_name='开发'),
    Groups(name='QA',full_name='Product Manager', cn_name='测试'),
    Groups(name='OP',full_name='Product Manager', cn_name='运维'),
    Groups(name='DBA',full_name='Product Manager', cn_name='数据库管理员'),
])

session.commit()

session.add_all([
    Users(name='Yangge', group_id=2),
    Users(name='Tom', group_id=2),
    Users(name='Rose', group_id=3),
    Users(name='shark', group_id=3),
    Users(name='xiguatian', group_id=5),
    Users(name='Jack', group_id=6),
    Users(name='new_user'),
])

session.commit()
```

补充

```python
print(session.query(Groups).join(Users).all())
```

1. 查询数据

问题一:  查询用户 `Shark`  是属于哪个组 ,打印出组名?

```python
user_obj = session.query(Users).filter_by(name='Shark').scalar()
print(user_obj.group.name)
scalar()`  假如存在，必须得到一个对象，不存在则返回 `None
```

问题二： 查询组 `RD` 组中的成员都有谁 ?

```python
group_obj = session.query(Groups).filter_by(name='RD').one()
for u_obj in group_obj.user:
    print(u_obj.name)
```

1. 更新数据

现在把用户 `new_user` 加入到 测试组 `QA`

```python
g_id = session.query(Groups.id).filter(Groups.name=='QA').scalar()
user_obj = session.query(Users).filter(Users.group_id == 1).update(
    {Users.group_id: g_id}
)
session.commit()
```

1. 删除数据

删除用户 `jack` , 之后在删除组 `DBA`

```python
# 删用户
session.query(Users).filter_by(name='jack').delete()
session.commit()
# 删组
query_obj = session.query(Groups).filter_by(name='DBA')
g_obj = query_obj.scalar()
u_obj = g_obj.user
if not u_obj:
    query_obj.delete()
    session.commit()
```

# 十、多对多 (扩展)

表示例

server

| id   | hostname | ip            |
| ---- | -------- | ------------- |
| 1    | web      | 172.16.153.10 |
| 2    | elk      | 172.16.153.10 |

sys_user

| id   | name  | password | key  |
| ---- | ----- | -------- | ---- |
| 1    | root  |          |      |
| 2    | shark |          |      |

host_to_sys_user

| id   | host_id | sys_user_id |
| ---- | ------- | ----------- |
| 1    | 1       | 1           |
| 2    | 1       | 2           |
| 3    | 2       | 1           |

1. 创建表

```python
# 多对多
# 一个服务器中可以有个用户
# 同名的用户可以出现在多台服务器中，也就是多台服务器可能存在相同的用户名
from sqlalchemy import create_engine
from sqlalchemy.ext.declarative import declarative_base

from sqlalchemy import Column, Integer, String, ForeignKey

# 需要安装 sqlalchemy-utils 和 passlib
# pip3 install sqlalchemy-utils passlib
from sqlalchemy_utils import IPAddressType, PasswordType

from sqlalchemy.orm import relationship

engine = create_engine("mysql+pymysql://root:123456@172.16.153.160:3306/db_1803",
                       echo=True,
                       max_overflow=5)
Base = declarative_base()

class ServersToSysUsers(Base):
    __tablename__ = 'servers_to_sys_users'
    nid = Column(Integer, primary_key=True, autoincrement=True)
    server_id = Column(Integer, ForeignKey('servers.id'))
    group_id = Column(Integer, ForeignKey('sys_users.id'))
    
    # 方式一
    #servers = relationship("Servers", backref='ser_to_user')
    #sys_users = relationship("SysUsers", backref='ser_to_user')

class Servers(Base):
    __tablename__ = 'servers'

    id = Column(Integer, primary_key=True, autoincrement=True)
    hostname = Column(String(64), unique=True, nullable=False)
    ip = Column(IPAddressType)  # 输入字符串，输出时是对象
    
    # 方式二
    sys_users = relationship('SysUsers',
                           secondary=ServersToSysUsers.__table__,
                           backref='servers')


class SysUsers(Base):
    __tablename__ = 'sys_users'
    id = Column(Integer, primary_key=True)
    name = Column(String(64), unique=True, nullable=False)
    password = Column(PasswordType(
        schemes=[
            'pbkdf2_sha512',
            'md5_crypt'
        ],
        deprecated=['md5_crypt']))
    key = Column(String(128),nullable=True)


Base.metadata.create_all(engine)
```

1. 添加数据

```python
session.add_all([
    Servers(hostname='web', ip='1.1.1.1'),
    Servers(hostname='elk', ip='1.1.1.2'),
    SysUsers(name='root',password='upsa'),
    SysUsers(name='shark',password='upsa'),
])
session.commit()

session.add_all([
    ServersToSysUsers(server_id=1,sys_users_id=1),
    ServersToSysUsers(server_id=1,sys_users_id=2),
    ServersToSysUsers(server_id=2,sys_users_id=1),
])
session.commit()
```

1. 查询数据

问题一： 查询服务器 `web` 下的所有用户名和密码

```python
# 方式一的查询
query = session.query(Servers).filter_by(hostname='web')
if query:
    for obj in query.scalar().ser_to_user:
        print(obj.sys_users.name)

# 方式二的查询
query = session.query(Servers).filter_by(hostname='web')
if query:
    for u_obj in query.scalar().sys_users:
        print(u_obj.name, u_obj.password)
```

问题二： 查询用户 `root` 存在于哪些服务器上

```python
# 方式一的查询 (作业练习)

# 方式二的查询：
query = session.query(SysUsers).filter_by(name='root')
if query:
    for server_obj in query.scalar().servers:
        print(server_obj.hostname,server_obj.ip)
```

1. 更新数据（作业练习）

增加一条服务器信息

主机名： mysql

IP 地址： 1.1.1.100

可以登录的账户： db_admin, select, db_op

同时完善其他表

1. 删除数据（作业练习）

删除服务器主机名为： `mysql` 的相关信息

# 十一、 扩展功能

使用 `sqlalchemy-utils` 会得到更多的功能

```python
pip3 install sqlalchemy-utils
```

**更多字段的数据类型**

http://sqlalchemy-utils.readthedocs.io/en/latest/data_types.html