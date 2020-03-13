---
title: Django---raw sql
date: 2019-09-18 21:39:47
tags:
---

实际开发中，在ORM模型查询API不够用的情况下，我们只能使用原始的SQL语句进行查询。

实际项目开发中，有时候我们需要使用原始的SQL语句进行查询。

9.18新添加一种方法，记录以备后用

<!--more-->

示例：

```python
# 1. extra

ret = models.Student.objects.all().extra(where=['id > %s'], params=['1'], order_by=['-id'])
# print(ret)
# for i in ret:
#     print(i)


# 2. raw

ret = models.Student.objects.raw('select * from main.app01_classes where id <= 2')
print(ret)
for i in ret:
    print(i.name)

# 3 connection
from django.db import connection, connections

# cursor = connection.cursor()
cursor = connections['db2'].cursor()
cursor.execute("""SELECT * from main.app01_classes where id > %s""", [1])
row = cursor.fetchall()
print(row)
```



### raw()方法

raw()管理器方法用于原始的SQL查询，并返回`模型的实例`。 需要注意的是：`raw()语法查询必须包含主键`。 这个方法执行原始的SQL查询，并返回一个django.db.models.query.RawQuerySet 实例。 这个RawQuerySet 实例可以像一般的QuerySet那样，通过迭代来提供对象实例。

#### 利用raw方法执行原生的SQL语句

假设现在我们有一个Book与Publish的Model：

```
class Book(models.Model):
    id = models.AutoField(primary_key=True)
    title = models.CharField(max_length=33)
    price = models.DecimalField(max_digits=8,decimal_places=2)
    publisher = models.ForeignKey(to='Publish',to_field='id',on_delete=models.CASCADE)
    
    def __str__(self):
        return self.title

class Publish(models.Model):
    id = models.AutoField(primary_key=True)
    name = models.CharField(max_length=22)
    city = models.CharField(max_length=20)
```

增加一条测试路由：

```
url(r'^raw/',views.raw),
```

然后使用raw方法：

```
for book_obj in Book.objects.raw('select * from book_book where price>100'):
    print(book_obj)
```

结果为书籍对象（这里用的是我的测试数据，并且__str__方法返回书籍的title，因此打印的结果是书籍的title）：

```
go
ruby
```

#### raw()查询可以查询其他表的数据

```
#注意这里必须select id字段，否则会报错
ret = Book.objects.raw('select id,city from book_publish')
for i in ret:
    print(i.id,i.city)
```

结果是Publish表的id与city：`注意必须要select id，否则会报错`

#### raw()方法自动将查询字段映射到模型字段

还可以通过translations参数指定一个把查询的字段名和ORM对象实例的字段名互相对应的字典：

```
dic = {'city':'C'}
    ret = Book.objects.raw('select * from book_publish',translations=dic)
    for i in ret:
        print(i.id,i.name,i.C)
```

结果为(这里用的是我自己的测试数据)：

```
21 苹果出版社 北京
22 橘子出版社 上海
23 樱桃出版社 成都
24 西瓜出版社 包头
25 橙子出版社 呼和浩特
```

#### 原生SQL还可以使用参数，注意不要自己使用字符串格式化拼接SQL语句，防止SQL注入！

**注意不要自己使用字符串格式化拼接SQL语句，防止SQL注入！**

```
dic = {'city':'C'}
ret = Book.objects.raw('select * from book_publish where id > %s',translations=dic,params=[22,])
for i in ret:
    print(i.id,i.name,i.C)
```

结果为：

```
23 樱桃出版社 成都
24 西瓜出版社 包头
25 橙子出版社 呼和浩特
```

### 直接执行自定义的SQL语句

**有时候raw()方法并不十分好用，很多情况下我们不需要将查询结果映射成模型，或者我们需要执行DELETE、 INSERT以及UPDATE操作。** **在这些情况下，我们可以直接访问数据库，完全避开模型层。** **我们可以直接从django提供的接口中获取数据库连接，然后像使用pymysql模块一样操作数据库。** **注意不要自己使用字符串格式化拼接SQL语句，防止SQL注入！** **实现的方法如下：**

```
from django.db import connection,connections

cursor = connection.cursor()  #cursor = connections['default'].cursor()
cursor.execute(' select * from book_publish where id > %s ',[22])
ret = cursor.fetchall()
print(ret)
```