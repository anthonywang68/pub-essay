---
title: oop编程
date: 
tags:
---

# 面对对象

## 一. 概述

- 面向过程：根据业务逻辑从上到下写垒代码
- 函数式：将某功能代码封装到函数中，日后便无需重复编写，仅调用函数即可
- 面向对象：对函数进行分类和封装，让开发“更快更好更强...”

面向过程编程(Object Oriented Programming，OOP，面向对象程序设计) 最易被初学者接受，其往往用一长段代码来实现指定功能，开发过程中最常见的操作就是粘贴复制，即：将之前实现的代码块复制到现需功能处。

<!--more-->

## 二. 创建类和对象

**面向对象编程**是一种编程方式，此编程方式的落地需要使用 “类” 和 “对象” 来实现，所以，面向对象编程其实就是对 “类” 和 “对象” 的使用。

**类**就是一个模板，模板里可以包含多个函数，函数里实现一些功能

**对象**则是根据模板创建的实例，通过实例对象可以执行类中的函数

![img](https://images0.cnblogs.com/blog2015/425762/201508/271420286872390.jpg)

> class是关键字，表示类
>
> 创建对象，类名称后加括号即可
>
> 类中的函数第一个参数必须是self（详细见：类的三大特性之封装）
>
> 类中定义的函数叫做 “方法”

```
# 创建类
class Foo:
     
    def Bar(self):
        print 'Bar'
 
    def Hello(self, name):
        print 'i am %s' %name
 
# 根据类Foo创建对象obj
obj = Foo()
obj.Bar()            #执行Bar方法
obj.Hello('wupeiqi') #执行Hello方法
```

## 三. 面向对象三大特征

### 封装

**第一步: 将内容封装到某处**
![img](https://images0.cnblogs.com/blog2015/425762/201508/271641407509817.jpg)

self 是一个形式参数，

当执行 obj1 = Foo('wupeiqi', 18 ) 时，self 等于 obj1

当执行 obj2 = Foo('alex', 78 ) 时，self 等于 obj2

所以，内容其实被封装到了对象 obj1 和 obj2 中，每个对象中都有 name 和 age 属性，在内存里类似于下图来保存。

**第二步: 从某处调用被封装的内容**

调用被封装的内容时, 有两种情况:

- 通过对象直接调用
- 通过self间接调用

1、通过对象直接调用被封装的内容

上图展示了对象 obj1 和 obj2 在内存中保存的方式，根据保存格式可以如此调用被封装的内容：对象.属性名

```
class Foo:
 
    def __init__(self, name, age):
        self.name = name
        self.age = age
 
obj1 = Foo('wupeiqi', 18)
print obj1.name    # 直接调用obj1对象的name属性
print obj1.age     # 直接调用obj1对象的age属性
 
obj2 = Foo('alex', 73)
print obj2.name    # 直接调用obj2对象的name属性
print obj2.age     # 直接调用obj2对象的age属性
```

2、通过self间接调用被封装的内容

执行类中的方法时，需要通过self间接调用被封装的内容

```
class Foo:
  
    def __init__(self, name, age):
        self.name = name
        self.age = age
  
    def detail(self):
        print self.name
        print self.age
  
obj1 = Foo('wupeiqi', 18)
obj1.detail()  # Python默认会将obj1传给self参数，即：obj1.detail(obj1)，所以，此时方法内部的 self ＝ obj1，即：self.name 是 wupeiqi ；self.age 是 18
  
obj2 = Foo('alex', 73)
obj2.detail()  # Python默认会将obj2传给self参数，即：obj1.detail(obj2)，所以，此时方法内部的 self ＝ obj2，即：self.name 是 alex ； self.age 是 78
```

综上所述,对于面对对象的封装来说,其实就是使用构造方法将内容封装到对象中, 然后通过对象直接或self间接获取被封装的内容.

**练习：游戏人生程序**

1、创建三个游戏人物，分别是：

苍井井，女，18，初始战斗力1000
东尼木木，男，20，初始战斗力1800
波多多，女，19，初始战斗力2500
2、游戏场景，分别：

草丛战斗，消耗200战斗力
自我修炼，增长100战斗力
多人游戏，消耗500战斗力

```
# -*- coding:utf-8 -*-

# #####################  定义实现功能的类  #####################

class Person:

    def __init__(self, na, gen, age, fig):
        self.name = na
        self.gender = gen
        self.age = age
        self.fight =fig

    def grassland(self):
        """注释：草丛战斗，消耗200战斗力"""

        self.fight = self.fight - 200

    def practice(self):
        """注释：自我修炼，增长100战斗力"""
        
        self.fight = self.fight + 200

    def incest(self):
        """注释：多人游戏，消耗500战斗力"""
        
        self.fight = self.fight - 500

    def detail(self):
        """注释：当前对象的详细情况"""

        temp = "姓名:%s ; 性别:%s ; 年龄:%s ; 战斗力:%s"  % (self.name, self.gender, self.age, self.fight)
        print temp

        
# #####################  开始游戏  #####################

cang = Person('苍井井', '女', 18, 1000)    # 创建苍井井角色
dong = Person('东尼木木', '男', 20, 1800)  # 创建东尼木木角色
bo = Person('波多多', '女', 19, 2500)      # 创建波多多角色

cang.incest() #苍井空参加一次多人游戏
dong.practice()#东尼木木自我修炼了一次
bo.grassland() #波多多参加一次草丛战斗


#输出当前所有人的详细情况
cang.detail()
dong.detail()
bo.detail()


cang.incest() #苍井空又参加一次多人游戏
dong.incest() #东尼木木也参加了一个多人游戏
bo.practice() #波多多自我修炼了一次

#输出当前所有人的详细情况
cang.detail()
dong.detail()
bo.detail()
```

### 继承

```
继承，面向对象中的继承和现实生活中的继承相同，即：子可以继承父的内容。

例如：

　　猫可以：喵喵叫、吃、喝、拉、撒

　　狗可以：汪汪叫、吃、喝、拉、撒

如果我们要分别为猫和狗创建一个类，那么就需要为 猫 和 狗 实现他们所有的功能，如下所示：
```

**伪代码**

```
    class 动物:

    def 吃(self):
        # do something

    def 喝(self):
        # do something

    def 拉(self):
        # do something

    def 撒(self):
        # do something

# 在类后面括号中写入另外一个类名，表示当前类继承另外一个类
class 猫(动物)：

    def 喵喵叫(self):
        print '喵喵叫'
        
# 在类后面括号中写入另外一个类名，表示当前类继承另外一个类
class 狗(动物)：

    def 汪汪叫(self):
        print '喵喵叫'
```

上述代码不难看出，吃、喝、拉、撒是猫和狗都具有的功能，而我们却分别的猫和狗的类中编写了两次。如果使用 继承 的思想，如下实现：

　　动物：吃、喝、拉、撒

　　 猫：喵喵叫（猫继承动物的功能）

　　 狗：汪汪叫（狗继承动物的功能）

**代码实例**

class Animal:

```
def eat(self):
    print "%s 吃 " %self.name

def drink(self):
    print "%s 喝 " %self.name

def shit(self):
    print "%s 拉 " %self.name

def pee(self):
    print "%s 撒 " %self.name
```

class Cat(Animal):

```
def __init__(self, name):
    self.name = name
    self.breed ＝ '猫'

def cry(self):
    print '喵喵叫'
```

class Dog(Animal):

```
def __init__(self, name):
    self.name = name
    self.breed ＝ '狗'
    
def cry(self):
    print '汪汪叫'
    

# ######### 执行 #########

class Animal:

    def eat(self):
        print "%s 吃 " %self.name

    def drink(self):
        print "%s 喝 " %self.name

    def shit(self):
        print "%s 拉 " %self.name

    def pee(self):
        print "%s 撒 " %self.name


class Cat(Animal):

    def __init__(self, name):
        self.name = name
        self.breed ＝ '猫'

    def cry(self):
        print '喵喵叫'

class Dog(Animal):
    
    def __init__(self, name):
        self.name = name
        self.breed ＝ '狗'
        
    def cry(self):
        print '汪汪叫'
        

# ######### 执行 #########

c1 = Cat('小白家的小黑猫')
c1.eat()

c2 = Cat('小黑的小白猫')
c2.drink()

d1 = Dog('胖子家的小瘦狗')
d1.eat()
```

所以，对于面向对象的继承来说，其实就是将多个类共有的方法提取到父类中，子类仅需继承父类而不必一一实现每个方法。

注：除了子类和父类的称谓，你可能看到过 派生类 和 基类 ，他们与子类和父类只是叫法不同而已。

![img](https://images0.cnblogs.com/blog2015/425762/201508/272229566093488.jpg)

学习了继承的写法之后，我们用代码来是上述阿猫阿狗的功能：

```
class Animal:

    def eat(self):
        print "%s 吃 " %self.name

    def drink(self):
        print "%s 喝 " %self.name

    def shit(self):
        print "%s 拉 " %self.name

    def pee(self):
        print "%s 撒 " %self.name


class Cat(Animal):

    def __init__(self, name):
        self.name = name
        self.breed ＝ '猫'

    def cry(self):
        print '喵喵叫'

class Dog(Animal):
    
    def __init__(self, name):
        self.name = name
        self.breed ＝ '狗'
        
    def cry(self):
        print '汪汪叫'
        

# ######### 执行 #########

c1 = Cat('小白家的小黑猫')
c1.eat()

c2 = Cat('小黑的小白猫')
c2.drink()

d1 = Dog('胖子家的小瘦狗')
d1.eat()
```

那么问题又来了，多继承呢？

是否可以继承多个类

如果继承的多个类每个类中都定了相同的函数，那么那一个会被使用呢？

1、Python的类可以继承多个类，Java和C#中则只能继承一个类

2、Python的类如果继承了多个类，那么其寻找方法的方式有两种，分别是：深度优先和广度优先
![img](https://images0.cnblogs.com/blog2015/425762/201508/272315068126604.jpg)

- 当类是经典类时，多继承情况下，会按照深度优先方式查找
- 当类是新式类时，多继承情况下，会按照广度优先方式查找

经典类和新式类，从字面上可以看出一个老一个新，新的必然包含了跟多的功能，也是之后推荐的写法，从写法上区分的话，如果 当前类或者父类继承了object类，那么该类便是新式类，否则便是经典类。

![img](https://images0.cnblogs.com/blog2015/425762/201508/272341313127410.jpg)

![img](https://images0.cnblogs.com/blog2015/425762/201508/272341553282314.jpg)

**经典类多继承**

```
class D:

    def bar(self):
        print 'D.bar'


class C(D):

    def bar(self):
        print 'C.bar'


class B(D):

    def bar(self):
        print 'B.bar'


class A(B, C):

    def bar(self):
        print 'A.bar'

a = A()
# 执行bar方法时
# 首先去A类中查找，如果A类中没有，则继续去B类中找，如果B类中么有，则继续去D类中找，如果D类中么有，则继续去C类中找，如果还是未找到，则报错
# 所以，查找顺序：A --> B --> D --> C
# 在上述查找bar方法的过程中，一旦找到，则寻找过程立即中断，便不会再继续找了
a.bar()
```

**新式类多继承**

```
class D(object):

    def bar(self):
        print 'D.bar'


class C(D):

    def bar(self):
        print 'C.bar'


class B(D):

    def bar(self):
        print 'B.bar'


class A(B, C):

    def bar(self):
        print 'A.bar'

a = A()
# 执行bar方法时
# 首先去A类中查找，如果A类中没有，则继续去B类中找，如果B类中么有，则继续去C类中找，如果C类中么有，则继续去D类中找，如果还是未找到，则报错
# 所以，查找顺序：A --> B --> C --> D
# 在上述查找bar方法的过程中，一旦找到，则寻找过程立即中断，便不会再继续找了
a.bar()
```

经典类：首先去A类中查找，如果A类中没有，则继续去B类中找，如果B类中么有，则继续去D类中找，如果D类中么有，则继续去C类中找，如果还是未找到，则报错

新式类：首先去A类中查找，如果A类中没有，则继续去B类中找，如果B类中么有，则继续去C类中找，如果C类中么有，则继续去D类中找，如果还是未找到，则报错

注意：在上述查找过程中，一旦找到，则寻找过程立即中断，便不会再继续找了

### 多态

Pyhon不支持Java和C#这一类强类型语言中多态的写法，但是原生多态，其Python崇尚“鸭子类型”。

**Python伪代码实现Jave或C#的多态**

```
class F1:
    pass


class S1(F1):

    def show(self):
        print 'S1.show'


class S2(F1):

    def show(self):
        print 'S2.show'


# 由于在Java或C#中定义函数参数时，必须指定参数的类型
# 为了让Func函数既可以执行S1对象的show方法，又可以执行S2对象的show方法，所以，定义了一个S1和S2类的父类
# 而实际传入的参数是：S1对象和S2对象

def Func(F1 obj):
    """Func函数需要接收一个F1类型或者F1子类的类型"""
    
    print obj.show()
    
s1_obj = S1()
Func(s1_obj) # 在Func函数中传入S1类的对象 s1_obj，执行 S1 的show方法，结果：S1.show

s2_obj = S2()
Func(s2_obj) # 在Func函数中传入Ss类的对象 ss_obj，执行 Ss 的show方法，结果：S2.show
```

**Python "鸭子类型"**

```
class F1:
    pass


class S1(F1):

    def show(self):
        print 'S1.show'


class S2(F1):

    def show(self):
        print 'S2.show'

def Func(obj):
    print obj.show()

s1_obj = S1()
Func(s1_obj) 

s2_obj = S2()
Func(s2_obj)
```

# Python - 面对对象(进阶)

## 类的成员

![img](https://images2015.cnblogs.com/blog/425762/201509/425762-20150916222236164-249943282.png)

### 一. 字段

字段包括：普通字段和静态字段，他们在定义和使用中有所区别，而最本质的区别是内存中保存的位置不同，

- 普通字段属于对象

- 静态字段属于类

  ```
        #### 字段的定义和使用
    class Province:
  
        # 静态字段
        country ＝ '中国'
  
        def __init__(self, name):
  
            # 普通字段
            self.name = name
  
  
    # 直接访问普通字段
    obj = Province('河北省')
    print obj.name
  
    # 直接访问静态字段
    Province.country
  ```

由上述代码可以看出【普通字段需要通过对象来访问】【静态字段通过类访问】，在使用上可以看出普通字段和静态字段的归属是不同的。其在内容的存储方式类似如下图：
![img](https://images2015.cnblogs.com/blog/425762/201509/425762-20150907094454965-329821364.jpg)

由上图可是：

静态字段在内存中只保存一份
普通字段在每个对象中都要保存一份
应用场景： 通过类创建对象时，如果每个对象都具有相同的字段，那么就使用静态字段

### 二. 方法

方法包括：普通方法、静态方法和类方法，三种方法在内存中都归属于类，区别在于调用方式不同。

- 普通方法：由对象调用；至少一个self参数；执行普通方法时，自动将调用该方法的对象赋值给self；
- 类方法：由类调用； 至少一个cls参数；执行类方法时，自动将调用该方法的类复制给cls；
- 静态方法：由类调用；无默认参数；

class Foo:

```
def __init__(self, name):
    self.name = name

def ord_func(self):
    """ 定义普通方法，至少有一个self参数 """

    # print self.name
    print '普通方法'

@classmethod
def class_func(cls):
    """ 定义类方法，至少有一个cls参数 """

    print '类方法'

@staticmethod
def static_func():
    """ 定义静态方法 ，无默认参数"""

    print '静态方法'


# 调用普通方法
f = Foo()
f.ord_func()

# 调用类方法
Foo.class_func()

# 调用静态方法
Foo.static_func()
```

![img](https://images2015.cnblogs.com/blog/425762/201510/425762-20151012145051507-726073921.jpg)

相同点：对于所有的方法而言，均属于类（非对象）中，所以，在内存中也只保存一份。

不同点：方法调用者不同、调用方法时自动传入的参数不同。

### 三. 属性

如果你已经了解Python类中的方法，那么属性就非常简单了，因为Python中的属性其实是普通方法的变种。

对于属性，有以下三个知识点：

- 属性的基本使用
- 属性的两种定义方式

**1. 属性的基本使用**

```
# ############### 定义 ###############
class Foo:

    def func(self):
        pass

    # 定义属性
    @property
    def prop(self):
        pass
# ############### 调用 ###############
foo_obj = Foo()

foo_obj.func()
foo_obj.prop   #调用属性
```

![img](https://images2015.cnblogs.com/blog/425762/201510/425762-20151019151817833-1751535998.png)

由属性的定义和调用要注意一下几点：

- 定义时，在普通方法的基础上添加 @property 装饰器；
- 定义时，属性仅有一个self参数
- 调用时，无需括号
  方法：foo_obj.func()
  属性：foo_obj.prop

注意：属性存在意义是：访问属性时可以制造出和访问字段完全相同的假象
属性由方法变种而来，如果Python中没有属性，方法完全可以代替其功能。

实例：对于主机列表页面，每次请求不可能把数据库中的所有内容都显示到页面上，而是通过分页的功能局部显示，所以在向数据库中请求数据时就要显示的指定获取从第m条到第n条的所有数据（即：limit m,n），这个分页的功能包括：

- 根据用户请求的当前页和总数据条数计算出 m 和 n

- 根据m 和 n 去数据库中请求数据

  ```
    # ############### 定义 ###############
    class Pager:
  
        def __init__(self, current_page):
            # 用户当前请求的页码（第一页、第二页...）
            self.current_page = current_page
            # 每页默认显示10条数据
            self.per_items = 10 
  
  
        @property
        def start(self):
            val = (self.current_page - 1) * self.per_items
            return val
  
        @property
        def end(self):
            val = self.current_page * self.per_items
            return val
  
    # ############### 调用 ###############
  
    p = Pager(1)
    p.start 就是起始值，即：m
    p.end   就是结束值，即：n
  ```

从上述可见，Python的属性的功能是：属性内部进行一系列的逻辑计算，最终将计算结果返回。

**2.属性的两种定义方式**

属性的定义有两种方式：

- 装饰器 即：在方法上应用装饰器
- 静态字段 即：在类中定义值为property对象的静态字段

由于新式类中具有三种访问方式，我们可以根据他们几个属性的访问特点，分别将三个方法定义为对同一个属性：获取、修改、删除
class Goods(object):

```
        def __init__(self):
            # 原价
            self.original_price = 100
            # 折扣
            self.discount = 0.8
    
        @property
        def price(self):
            # 实际价格 = 原价 * 折扣
            new_price = self.original_price * self.discount
            return new_price
    
        @price.setter
        def price(self, value):
            self.original_price = value
    
        @price.deltter
        def price(self, value):
            del self.original_price

    obj = Goods()
    obj.price         # 获取商品价格
    obj.price = 200   # 修改商品原价
    del obj.price     # 删除商品原价
```

静态字段方式，创建值为property对象的静态字段:

property的构造方法中有个四个参数

第一个参数是方法名，调用 对象.属性 时自动触发执行方法
第二个参数是方法名，调用 对象.属性 ＝ XXX 时自动触发执行方法
第三个参数是方法名，调用 del 对象.属性 时自动触发执行方法
第四个参数是字符串，调用 对象.属性.__doc__ ，此参数是该属性的描述信息

```
class Goods(object):

    def __init__(self):
        # 原价
        self.original_price = 100
        # 折扣
        self.discount = 0.8

    def get_price(self):
        # 实际价格 = 原价 * 折扣
        new_price = self.original_price * self.discount
        return new_price

    def set_price(self, value):
        self.original_price = value

    def del_price(self, value):
        del self.original_price

    PRICE = property(get_price, set_price, del_price, '价格属性描述...')

obj = Goods()
obj.PRICE         # 获取商品价格
obj.PRICE = 200   # 修改商品原价
del obj.PRICE     # 删除商品原价
```

## 类的修饰符

类的所有成员在上一步骤中已经做了详细的介绍，对于每一个类的成员而言都有两种形式：

- 公有成员，在任何地方都能访问
- 私有成员，只有在类的内部才能方法

私有成员和公有成员的定义不同：私有成员命名时，前两个字符是下划线。（特殊成员除外，例如：**init**、**call**、__dict__等）

```
class C:
 
    def __init__(self):
        self.name = '公有字段'
        self.__foo = "私有字段"
```

私有成员和公有成员的访问限制不同：

静态字段

- 公有静态字段：类可以访问；类内部可以访问；派生类中可以访问

- 私有静态字段：仅类内部可以访问；

  ```
    class C:
  
        name = "公有静态字段"
  
        def func(self):
            print C.name
  
    class D(C):
  
        def show(self):
            print C.name
  
  
    C.name         # 类访问
  
    obj = C()
    obj.func()     # 类内部可以访问
  
    obj_son = D()
    obj_son.show() # 派生类中可以访问
    *******************************
  
    class C:
  
        __name = "公有静态字段"
  
        def func(self):
            print C.__name
  
    class D(C):
  
        def show(self):
            print C.__name
  
  
    C.__name       # 类访问            ==> 错误
  
    obj = C()
    obj.func()     # 类内部可以访问     ==> 正确
  
    obj_son = D()
    obj_son.show() # 派生类中可以访问   ==> 错误
  ```

普通字段

- 公有普通字段：对象可以访问；类内部可以访问；派生类中可以访问

- 私有普通字段：仅类内部可以访问；
  ps：如果想要强制访问私有字段，可以通过 【对象._类名__私有字段明 】访问（如：obj._C__foo），不建议强制访问私有成员。

  ```
    class C:
  
        def __init__(self):
            self.foo = "公有字段"
  
        def func(self):
            print self.foo 　#　类内部访问
  
    class D(C):
  
        def show(self):
            print self.foo　＃　派生类中访问
  
    obj = C()
  
    obj.foo     # 通过对象访问
    obj.func()  # 类内部访问
  
    obj_son = D();
    obj_son.show()  # 派生类中访问
  
    **************************
  
    class C:
  
        def __init__(self):
            self.__foo = "私有字段"
  
        def func(self):
            print self.foo 　#　类内部访问
  
    class D(C):
  
        def show(self):
            print self.foo　＃　派生类中访问
  
    obj = C()
  
    obj.__foo     # 通过对象访问    ==> 错误
    obj.func()  # 类内部访问        ==> 正确
  
    obj_son = D();
    obj_son.show()  # 派生类中访问  ==> 错误
  ```

方法、属性的访问于上述方式相似，即：私有成员只能在类内部使用

ps：非要访问私有属性的话，可以通过 对象._类__属性名

## 类的特殊成员

上文介绍了Python的类成员以及成员修饰符，从而了解到类中有字段、方法和属性三大类成员，并且成员名前如果有两个下划线，则表示该成员是私有成员，私有成员只能由类内部调用。无论人或事物往往都有不按套路出牌的情况，Python的类成员也是如此，存在着一些具有特殊含义的成员，详情如下：

1.__ doc __

　　表示类的描述信息

```
class Foo:
    """ 描述类信息，这是用于看片的神奇 """

    def func(self):
        pass

print Foo.__doc__
#输出：类的描述信息
```

2.__ module __ 和 __ class __

　　__ module __ 表示当前操作的对象在那个模块

　　__ class__ 表示当前操作的对象的类是什么

```
#!/usr/bin/env python
# -*- coding:utf-8 -*-

class C:

    def __init__(self):
        self.name = 'wupeiqi'


from lib.aa import C

obj = C()
print obj.__module__  # 输出 lib.aa，即：输出模块
print obj.__class__      # 输出 lib.aa.C，即：输出类
```

3.__ init __

构造方法，通过类创建对象时，自动触发执行。

```
class Foo:

    def __init__(self, name):
        self.name = name
        self.age = 18


obj = Foo('wupeiqi') # 自动执行类中的 __init__ 方法
```

4.__ del __

析构方法，当对象在内存中被释放时，自动触发执行。

注：此方法一般无须定义，因为Python是一门高级语言，程序员在使用时无需关心内存的分配和释放，因为此工作都是交给Python解释器来执行，所以，析构函数的调用是由解释器在进行垃圾回收时自动触发执行的。

```
class Foo:

    def __del__(self):
        pass
```

5.__ call __

对象后面加括号，触发执行。

注：构造方法的执行是由创建对象触发的，即：对象 = 类名() ；而对于 **call** 方法的执行是由对象后加括号触发的，即：对象() 或者 类()()

```
class Foo:

    def __init__(self):
        pass
    
    def __call__(self, *args, **kwargs):

        print '__call__'


obj = Foo() # 执行 __init__
obj()       # 执行 __call__
```

6.__dict __

类或对象中的所有成员

上文中我们知道：类的普通字段属于对象；类中的静态字段和方法等属于类，即：

![img](https://images2015.cnblogs.com/blog/425762/201509/425762-20150907094454965-329821364.jpg)

```
class Province:

    country = 'China'

    def __init__(self, name, count):
        self.name = name
        self.count = count

    def func(self, *args, **kwargs):
        print 'func'

# 获取类的成员，即：静态字段、方法、
print Province.__dict__
# 输出：{'country': 'China', '__module__': '__main__', 'func': <function func at 0x10be30f50>, '__init__': <function __init__ at 0x10be30ed8>, '__doc__': None}

obj1 = Province('HeBei',10000)
print obj1.__dict__
# 获取 对象obj1 的成员
# 输出：{'count': 10000, 'name': 'HeBei'}

obj2 = Province('HeNan', 3888)
print obj2.__dict__
# 获取 对象obj1 的成员
# 输出：{'count': 3888, 'name': 'HeNan'}
```

7.__ str __

如果一个类中定义了__str__方法，那么在打印 对象 时，默认输出该方法的返回值。

```
class Foo:

    def __str__(self):
        return 'wupeiqi'


obj = Foo()
print obj
# 输出：wupeiqi
```

8.__ getitem __ ,__ setitem__ ,__ delitem __

```
#!/usr/bin/env python
# -*- coding:utf-8 -*-
 
class Foo(object):
 
    def __getitem__(self, key):
        print '__getitem__',key
 
    def __setitem__(self, key, value):
        print '__setitem__',key,value
 
    def __delitem__(self, key):
        print '__delitem__',key
 
 
obj = Foo()
 
result = obj['k1']      # 自动触发执行 __getitem__
obj['k2'] = 'wupeiqi'   # 自动触发执行 __setitem__
del obj['k1']           # 自动触发执行 __delitem__
```

9.__ getslice __ __ setslice __ __ delslice __

该三个方法用于分片操作，如：列表

```
#!/usr/bin/env python
# -*- coding:utf-8 -*-
 
class Foo(object):
 
    def __getslice__(self, i, j):
        print '__getslice__',i,j
 
    def __setslice__(self, i, j, sequence):
        print '__setslice__',i,j
 
    def __delslice__(self, i, j):
        print '__delslice__',i,j
 
obj = Foo()
 
obj[-1:1]                   # 自动触发执行 __getslice__
obj[0:1] = [11,22,33,44]    # 自动触发执行 __setslice__
del obj[0:2]                # 自动触发执行 __delslice__
```

10.__ iter __

用于迭代器，之所以列表、字典、元组可以进行for循环，是因为类型内部定义了 **iter**

```
第一步

class Foo(object):
    pass


obj = Foo()

for i in obj:
    print i
    
# 报错：TypeError: 'Foo' object is not iterable

第二步

#!/usr/bin/env python
# -*- coding:utf-8 -*-

class Foo(object):
    
    def __iter__(self):
        pass

obj = Foo()

for i in obj:
    print i

# 报错：TypeError: iter() returned non-iterator of type 'NoneType'

第三步

#!/usr/bin/env python
# -*- coding:utf-8 -*-

class Foo(object):

    def __init__(self, sq):
        self.sq = sq

    def __iter__(self):
        return iter(self.sq)

obj = Foo([11,22,33,44])

for i in obj:
    print i
```

以上步骤可以看出，for循环迭代的其实是 iter([11,22,33,44]) ，所以执行流程可以变更为：

```
#!/usr/bin/env python
# -*- coding:utf-8 -*-
 
obj = iter([11,22,33,44])
 
for i in obj:
    print i
```

for循环语法内部

```
#!/usr/bin/env python
# -*- coding:utf-8 -*-

obj = iter([11,22,33,44])

while True:
    val = obj.next()
    print val
```

上述代码中，obj 是通过 Foo 类实例化的对象，其实，不仅 obj 是一个对象，Foo类本身也是一个对象，因为在Python中一切事物都是对象。

如果按照一切事物都是对象的理论：obj对象是通过执行Foo类的构造方法创建，那么Foo类对象应该也是通过执行某个类的 构造方法 创建。

```
print type(obj) # 输出：<class '__main__.Foo'>     表示，obj 对象由Foo类创建
print type(Foo) # 输出：<type 'type'>              表示，Foo类对象由 type 类创建
```

所以，obj对象是Foo类的一个实例，Foo类对象是 type 类的一个实例，即：Foo类对象 是通过type类的构造方法创建。

那么，创建类就可以有两种方式：

a). 普通方式

```
class Foo(object):
 
    def func(self):
        print 'hello wupeiqi'
```

b).特殊方式（type类的构造函数）

```
def func(self):
    print 'hello wupeiqi'

Foo = type('Foo',(object,), {'func': func})
#type第一个参数：类名
#type第二个参数：当前类的基类
#type第三个参数：类的成员
```

＝＝》 类 是由 type 类实例化产生

那么问题来了，类默认是由 type 类实例化产生，type类中如何实现的创建类？类又是如何创建对象？

答：类中有一个属性 **metaclass**，其用来表示该类由 谁 来实例化创建，所以，我们可以为 **metaclass** 设置一个type类的派生类，从而查看 类 创建的过程。

![img](https://images2015.cnblogs.com/blog/425762/201510/425762-20151020005415755-634423846.png)

```
class MyType(type):

    def __init__(self, what, bases=None, dict=None):
        super(MyType, self).__init__(what, bases, dict)

    def __call__(self, *args, **kwargs):
        obj = self.__new__(self, *args, **kwargs)

        self.__init__(obj)

class Foo(object):

    __metaclass__ = MyType

    def __init__(self, name):
        self.name = name

    def __new__(cls, *args, **kwargs):
        return object.__new__(cls, *args, **kwargs)

# 第一阶段：解释器从上到下执行代码创建Foo类
# 第二阶段：通过Foo类创建obj对象
obj = Foo()
```

# Python - 面对对象(其他相关,异常处理,反射,等..)

## 一、isinstance(obj, cls)

检查是否obj是否是类 cls 的对象

```
class Foo(object):
    pass
 
obj = Foo()
 
isinstance(obj, Foo)
```

## 二、issubclass(sub, super)

检查sub类是否是 super 类的派生类

```
class Foo(object):
    pass
 
class Bar(Foo):
    pass
 
issubclass(Bar, Foo)
```

## 三、异常处理

### 1. 异常处理

在编程过程中为了增加友好性，在程序出现bug时一般不会将错误信息显示给用户，而是现实一个提示的页面，通俗来说就是不让用户看见大黄页！！！

```
try:
    pass
except Exception,ex:
    pass
```

需求：将用户输入的两个数字相加

```
while True:
    num1 = raw_input('num1:')
    num2 = raw_input('num2:')
    try:
        num1 = int(num1)
        num2 = int(num2)
        result = num1 + num2
    except Exception, e:
        print '出现异常，信息如下：'
        print e
```

### 2.异常种类

python中的异常种类非常多，每个异常专门用于处理某一项异常！！！

```
常用的异常

AttributeError 试图访问一个对象没有的树形，比如foo.x，但是foo没有属性x
IOError 输入/输出异常；基本上是无法打开文件
ImportError 无法引入模块或包；基本上是路径问题或名称错误
IndentationError 语法错误（的子类） ；代码没有正确对齐
IndexError 下标索引超出序列边界，比如当x只有三个元素，却试图访问x[5]
KeyError 试图访问字典里不存在的键
KeyboardInterrupt Ctrl+C被按下
NameError 使用一个还未被赋予对象的变量
SyntaxError Python代码非法，代码不能编译(个人认为这是语法错误，写错了）
TypeError 传入对象类型与要求的不符合
UnboundLocalError 试图访问一个还未被设置的局部变量，基本上是由于另有一个同名的全局变量，
导致你以为正在访问它
ValueError 传入一个调用者不期望的值，即使值的类型是正确的
```

**实例**

```
dic = ["wupeiqi", 'alex']
try:
    dic[10]
except IndexError, e:
    print e


dic = {'k1':'v1'}
try:
    dic['k20']
except KeyError, e:
    print e

s1 = 'hello'
try:
    int(s1)
except ValueError, e:
    print e
```

对于上述实例，异常类只能用来处理指定的异常情况，如果非指定异常则无法处理。

```
 未捕获到异常，程序直接报错
 
s1 = 'hello'
try:
    int(s1)
except IndexError,e:
    print e
```

所以，写程序时需要考虑到try代码块中可能出现的任意异常，可以这样写：

```
s1 = 'hello'
try:
    int(s1)
except IndexError,e:
    print e
except KeyError,e:
    print e
except ValueError,e:
    print e
```

万能异常 在python的异常中，有一个万能异常：Exception，他可以捕获任意异常，即：

```
s1 = 'hello'
try:
    int(s1)
except Exception,e:
    print e
```

接下来你可能要问了，既然有这个万能异常，其他异常是不是就可以忽略了！

答：当然不是，对于特殊处理或提醒的异常需要先定义，最后定义Exception来确保程序正常运行。

```
s1 = 'hello'
try:
    int(s1)
except KeyError,e:
    print '键错误'
except IndexError,e:
    print '索引错误'
except Exception, e:
    print '错误'
```

### 3.异常其他结构

```
try:
    # 主代码块
    pass
except KeyError,e:
    # 异常时，执行该块
    pass
else:
    # 主代码块执行完，执行该块
    pass
finally:
    # 无论异常与否，最终执行该块
    pass
```

### 4.主动触发异常

```
try:
    raise Exception('错误了。。。')
except Exception,e:
    print e
```

### 5.自定义异常

```
class WupeiqiException(Exception):
 
    def __init__(self, msg):
        self.message = msg
 
    def __str__(self):
        return self.message
 
try:
    raise WupeiqiException('我的异常')
except WupeiqiException,e:
    print e
```

### 6.断言

```
# assert 条件
 
assert 1 == 1
 
assert 1 == 2
```

## 四、反射

python中的反射功能是由以下四个内置函数提供：hasattr、getattr、setattr、delattr，改四个函数分别用于对对象内部执行：检查是否含有某成员、获取成员、设置成员、删除成员。

```
class Foo(object):
 
    def __init__(self):
        self.name = 'wupeiqi'
 
    def func(self):
        return 'func'
 
obj = Foo()
 
# #### 检查是否含有成员 ####
hasattr(obj, 'name')
hasattr(obj, 'func')
 
# #### 获取成员 ####
getattr(obj, 'name')
getattr(obj, 'func')
 
# #### 设置成员 ####
setattr(obj, 'age', 18)
setattr(obj, 'show', lambda num: num + 1)
 
# #### 删除成员 ####
delattr(obj, 'name')
delattr(obj, 'func')
```

**类是对象**

```
class Foo(object):
 
    staticField = "old boy"
 
    def __init__(self):
        self.name = 'wupeiqi'
 
    def func(self):
        return 'func'
 
    @staticmethod
    def bar():
        return 'bar'
 
print getattr(Foo, 'staticField')
print getattr(Foo, 'func')
print getattr(Foo, 'bar')
```

**反射也是对象**

```
#!/usr/bin/env python
# -*- coding:utf-8 -*-
 
"""
程序目录：
    home.py
    index.py
 
当前文件：
    index.py
"""
 
 
import home as obj
 
#obj.dev()
 
func = getattr(obj, 'dev')
func() 
```

## 五. 单例模式

设计模式之单例模式

单例设计模式是怎么来的？
在面向对象的程序设计中，当业务并发量非常大时，那么就会出现重复创建相同的对象，每创建一个对象就会开辟一块内存空间，而这些对象其实是一模一样的，那么有没有办法使用得内存对象只创建一次，然后再随处使用呢？单例模式就是为了解决这个问题而产生的。

实现方式：
１、创建一个类静态字段（类变量）__instance
２、创建一个静态函数，通过函数的逻辑判断 __instance 是否已存在，如不存在就将对象值赋于__instance，即__instance = 类(),否则直接返回__instance，也即创建的对象都是一样的
３、使用单例模式创建对象时直接通过类调用静态函数创建即可

```
#普通模式
class A(object):
    def __init__(self,name,male):
        self.name = name
        self.name = male

#实例化多个对象
obj1 = A('ben','boy')
obj2 = A('min','girl')
obj3 = A('miao','boy')
##打印内存地址，可以看到内存地址都是不一样的
print id(obj1),id(obj2),id(obj3)

#单例模式
class A(object):
    __instance = None
    def __init__(self,name,male):
        self.name = name
        self.name = male
    @staticmethod
    def create_obj():
        if not A.__instance:
            A.__instance = A('ben','boy')
            return A.__instance
        else:
            return A.__instance

#单例模式实例化多个对象
obj1 = A.create_obj()
obj2 = A.create_obj()
obj3 = A.create_obj()
##打印内存地址，可以看到内存地址都是一样的
print id(obj1),id(obj2),id(obj3)
```

result :

```
1 >>> #普通模式
 2 ... class A(object):
 3 ...     def __init__(self,name,male):
 4 ...         self.name = name
 5 ...         self.name = male
 6 ... 
 7 >>> #实例化多个对象
 8 ... obj1 = A('ben','boy')
 9 >>> obj2 = A('min','girl')
10 >>> obj3 = A('miao','boy')
11 >>> ##打印内存地址，可以看到内存地址都是不一样的
12 ... print id(obj1),id(obj2),id(obj3)
13 140230687882448 140230687882512 140230687882576
14 >>> 
15 >>> #单例模式
16 ... class A(object):
17 ...     __instance = None
18 ...     def __init__(self,name,male):
19 ...         self.name = name
20 ...         self.name = male
21 ...     @staticmethod
22 ...     def create_obj():
23 ...         if not A.__instance:
24 ...             A.__instance = A('ben','boy')
25 ...             return A.__instance
26 ...         else:
27 ...             return A.__instance
28 ... 
29 >>> #单例模式实例化多个对象
30 ... obj1 = A.create_obj()
31 >>> obj2 = A.create_obj()
32 >>> obj3 = A.create_obj()
33 >>> ##打印内存地址，可以看到内存地址都是一样的
34 ... print id(obj1),id(obj2),id(obj3)
35 140230687882832 140230687882832 140230687882832
36 >>>
```