---
title: python add little summary
date: 
tags:
---

# python - 那些零碎的知识点

## 一. 字符串格式化

### 1. "旧式字符串解析(%操作符)"

```repl
'Hello, %s' % name
"Hello, Bob"


'Hey %(name)s, there is a 0x%(errno)x error!' % {
"name": name, "errno": errno }
'Hey Bob, there is a 0xbadc0ffee error!'
```

### 2. "新式"字符串格式化(str.format)

```
'Hello, {}'.format(name)
'Hello, Bob'


'Hey {name}, there is a 0x{errno:x} error!'.format(     name=name, errno=errno)
'Hey Bob, there is a 0xbadc0ffee error!'
```

<!--more-->

### 3. 字符串插值/f-Strings(Python 3.6+)

python 3.6新出的,本人用了这个之后, 果断抛弃其他方法, 真的太强大了!!!

```
name = Bob
f'Hello, {name}!'
'Hello, Bob!'


def greet(name, question):
...     return f"Hello, {name}! How's it {question}?"
...
greet('Bob', 'going')
"Hello, Bob! How's it going?"
```

### 4. 字符串模板法(Python标准库)

```
templ_string = 'Hey $name, there is a $error error!'

Template(templ_string).substitute(

...     name=name, error=hex(errno))

'Hey Bob, there is a 0xbadc0ffee error!'
```

## 二. 小数据池

### 1. id, is, == =

```
id  => id是内存地址

is  => 比较两边的内存是否相等

==  => 比较两边的数值是否相等

=   => 是赋值
```

### 2. 小数据池的缓存机制

> 小数据池，也称为小整数缓存机制，或者称为驻留机制等等，

> 对于数字: -5~256是会被加到⼩小数据池中的. 每次使⽤用都是同⼀一个对象.
>
> 对于字符串串:
>
> 1.如果字符串串的长度是0或者1, 都会默认进行缓存
>
> 2.字符串长度大于1, 但是字符串中只包含字母, 数字, 下划线时才会缓存
>
> 3.用乘法的到的字符串乘数为1, 仅包含数字,字母,下划线时会被缓存. 如果
> 包含其他字符, 而长度<=1 也会被驻存,乘数大于1 . 仅包含数字, 字母, 下划
> 线这个时候会被缓存. 但字符串串长度不能大于20
>
> 4.指定驻留留. 我们可以通过sys模块中的intern()函数来指定要驻留留的内容.

## 三. 深浅拷贝

> copy浅拷贝，没有拷贝子对象，所以原始数据改变，子对象会改变
>
> 深拷贝，包含对象里面的自对象的拷贝，所以原始对象的改变不会造成深拷贝里任何子元素的改变

```
import copy
a = [1,23,66,[6,8]]

d = a
b = copy.copy(a)
c = copy.deepcopy(a)
a.append(99)#[1, 23, 66, [6, 8], 99] [1, 23, 66, [6, 8]] [1, 23, 66, [6, 8]] [1, 23, 66, [6, 8], 99]
print(a,b,c,d)
a[3].append(88)#[1, 23, 66, [6, 8, 88], 99] [1, 23, 66, [6, 8, 88]] [1, 23, 66, [6, 8]] [1, 23, 66, [6, 8, 88], 99]
print(a,b,c,d)
```

## 垃圾回收机制

> 一. Python内部使用引用计数机制, 来保持追踪内存中的对象,所有对象都有引用计数. 一个对象分配一个新名称,然后将其放入到一个容器中(如列表, 元祖, 字典), 这样这个计数就增加. 当我们使用delect删除语句对对象别名进行删除或者,引用超过了这个作用域,或者被重新复制的时候,引用的计数会减少.对于不可变数据(数字,字符串)解释器会在程序的不同部分共享内存,以便节约内存.sys.getrefcount( )函数可以获得对象的当前引用计数
>
> 二. 这个垃圾回收机制呢, 就是当一个对象的引用计数归零时,他就会被垃圾回收机制处理掉 ,当两个对象相互引用的时候, del语句可以减少 引用次数并销毁引用底层对象的名称, 由于每个对象都包含一个对其他对象的引用, 因此引用计数不会归零, 对象也不会销毁,为解决这一问题，解释器会定期执行一个循环检测器，搜索不可访问对象的循环并删除它们。
>
> 三.内存池机制
>
> Python提供了对内存的垃圾收集机制，但是它将不用的内存放到内存池而不是返回给操作系统。
>
> 1，Pymalloc机制。为了加速Python的执行效率，Python引入了一个内存池机制，用于管理对小块内存的申请和释放。
>
> 2，Python中所有小于256个字节的对象都使用pymalloc实现的分配器，而大的对象则使用系统的malloc。
>
> 3，对于Python对象，如整数，浮点数和List，都有其独立的私有内存池，对象间不共享他们的内存池。也就是说如果你分配又释放了大量的整数，用于缓存这些整数的内存就不能再分配给浮点数。

## 哈希

> Hash（哈希） 一般译为“散列”，它是一种加密过的存储结构。它将任意长度的输入，通过散列算法，转换成固定长度的输出。而这个输出就是散列的值。并且，这个转换过程是一种压缩映射，所以，散列值的空间远小于输入值的空间。另外，不同的输入可能会散列出相同的输出，所以不能从散列值来确定其输入值是什么。hash应用中一个简单的例子就是短链接。短链接是一个hash后的散列值，它的输入值就是原始链接。首先，我们拿到一个原始链接，到一个具有hash功能的服务器上，做hash转换，得到一个散列值，这个值就是短链接的值。前面说到过，散列值会远小于输入值，所以短链接比原链接字符长度小了许多。然后，再将这个短链接与原链接存到服务器上的数据库中，形成映射关系。当有人访问服务器上的短链接时，只需要从映射关系中找到原始链接，即可跳转到原始链接。
> Hash 的一个特点就是性能好，查询起来很快。它是一种以空间换取时间的方案。

简单说,如果一个对象是可哈希的, 那么生命周期内这个对象不可变,如:int,float,string, tuple.
反之, 不可哈希的对象可变, 像lsit, dict, set.







在[Python](http://lib.csdn.net/base/python)的实际开发中，很多都需要用到字符串拼接，[python](http://lib.csdn.net/base/python)中字符串拼接有很多，今天总结一下：

- 用`+`符号拼接
- 用`%`符号拼接
- 用`join()`方法拼接
- 用`format()`方法拼接
- 用`string`模块中的`Template`对象

如果还有其他方法，欢迎补充。 
例子：

```
fruit1 = 'apples'
fruit2 = 'bananas'
fruit3 = 'pears'
```

 

要求： 
输出字符串’There are apples, bananas, pears on the table’

#### **1. 用+符号拼接**

用`+`拼接字符串如下： 

```
1 str = 'There are'+fruit1+','+fruit2+','+fruit3+' on the table' 
```

该方法效率比较低，不建议使用

#### **2. 用%符号拼接**

用`%`符号拼接方法如下： 

```
1 str = 'There are %s, %s, %s on the table.' % (fruit1,fruit2,fruit3) 
```

除了用元组的方法，还可以使用字典如下： 

```
1 str = 'There are %(fruit1)s,%(fruit2)s,%(fruit3)s on the table' % {'fruit1':fruit1,'fruit2':fruit2,'fruit3':fruit3} 
```

该方法比较通用

#### **3. 用join()方法拼接**

join()`方法拼接如下

```
1 temp = ['There are ',fruit1,',',fruit2,',',fruit3,' on the table']
2 ''.join(temp)
```

该方法使用与序列操作

#### **4. 用format()方法拼接**

用`format()`方法拼接如下:

#### **4. 用format()方法拼接**

用`format()`方法拼接如下:

```
1 str = 'There are {}, {}, {} on the table'
2 str.format(fruit1,fruit2,fruit3)
```

 

还可以指定参数对应位置：

```
1 str = 'There are {2}, {1}, {0} on the table'
2 str.format(fruit1,fruit2,fruit3) #fruit1出现在0的位置
```

 

同样，也可以使用字典：

```
1 str = 'There are {fruit1}, {fruit2}, {fruit3} on the table'
2 str.format(fruit1=fruit1,fruit2=fruit2,fruit3=fruit3)
```

#### **5. 用string模块中的Template对象**

用`string`模块中的`Template`对象如下：

```
1 from string import Template
2 str = Template('There are ${fruit1}, ${fruit2}, ${fruit3} on the table') #此处用的是{}，别搞错了哦
3 str.substitute(fruit1=fruit1,fruit2=fruit2,fruit3=fruit3) #如果缺少参数，或报错如果使用safe_substitute()方法不会
4 str.safe_substitute(fruit1=fruit1,fruit2=fruit2) 
5 #输出'There are apples, bananas, ${fruit3} on the table'
```

