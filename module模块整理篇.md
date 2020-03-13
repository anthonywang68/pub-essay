---
title: module模块整理篇
date: 2019-09-06 09:59:04
tags:
---

# Python - 模块(一)

## 模块的引用方式

1. import 语句

   > 执行对应文件
   >
   > 引入变量名

   import time #导入时间模块,也可以同时导入多个模块,用逗号隔开

2. from ... import 模块名

   ```
    from my_model import main
   ```

3. from ... import *

   ```
    引入一切，即导入模块中所有的项目，不推荐使用。因为引入的其它来源的命名，很可能覆盖了已有的定义。
   ```

## 常用模块

<!--more-->

```
### time ###
```

时间相关的操作, 时间有三种表示方法:

- 时间戳(timestamp): 时间戳使用的是从1970年1月1日00点00分00秒得到现在一共经过了多少秒..用float表示.
- 格式化的字符串(strftime): 这个时间可以根据我们的需要对时间进行任意的格式化.
- 结构化时间(struct_time): 这个时间主要可以把时间进行分类规划,比如,1970年01月01日 00点00分00秒 这个时间可以被细分为年,月,日..

**生成时间戳**

```
1. time.time()
输出 1515137389.69163
```

**生成格式化的时间字符串**

```
1. time.ctime()
输出 Fri Jan  5 15:34:00 2018
=============================
2. time.strftime("%Y-%m-%d %H:%M:%S")
输出 2018-01-05 15:34:27
========================
```

**生成结构化时间**

```
1. time.gmtime()
输出 time.struct_time(tm_year=2018, tm_mon=1, tm_mday=5, tm_hour=7, tm_min=34, tm_sec=57, tm_wday=4, tm_yday=5, tm_isdst=0)
===========================================================================================================================
2. time.localtime()
输出 time.struct_time(tm_year=2018, tm_mon=1, tm_mday=5, tm_hour=15, tm_min=35, tm_sec=59, tm_wday=4, tm_yday=5, tm_isdst=0)
============================================================================================================================
```

**时间戳转格式化时间**

```
t = time.localtime(188888888)   # 结构化时间
s = time.strftime("%Y-%m-%d %H:%M:%S", t)   # 格式化这个时间
print(s)
```

**格式化时间转化为时间戳**

```
s = "2020-10-01 12:18:12"
t = time.strptime(s, "%Y-%m-%d %H:%M:%S")   # 转化成结构化时间
print(time.mktime(t))   # 转化成时间戳
```

**计算时间差**

```
import time
ture_time=time.mktime(time.strptime('2017-09-11 08:30:00'), "%Y-%m-%d %H:%M:%S")

time_now=time.mktime(time.strptime('2017-11-11 08:30:00'), "%Y-%m-%d %H:%M:%S")
dif_time=time_now-treu_time
stuct_time=time.localtime(dif_time)
```

**datetime**

```
import datetime
'''
datetime.date：表示日期的类。常用的属性有year, month, day
datetime.time：表示时间的类。常用的属性有hour, minute, second, microsecond
datetime.datetime：表示日期时间
datetime.timedelta：表示时间间隔，即两个时间点之间的长度
timedelta([days[, seconds[, microseconds[, milliseconds[, minutes[, hours[, weeks]]]]]]])
strftime("%Y-%m-%d")
'''
import datetime
print datetime.datetime.now()
print datetime.datetime.now() - datetime.timedelta(days=5)
```

![img](https://images2015.cnblogs.com/blog/1060991/201612/1060991-20161213213359745-1291787525.png)

### random(随机模块)

```
import random                 
print(random.random())         # 0到1的随机浮点数
print(random.randint(1,3))     # 整形 闭区间 [1,3]
print(random.randrange(1,3))   # 整形，开区间 [1,3)
li = [11,22,33,44,55]
print(random.choice(li))       # 基于可迭代对象随机
print(random.sample(li,2))     # 随机选定多个
print(random.uniform(1,2))     # 任意范围的浮点型
random.shuffle(li)             # 随机打乱次序
print(li)
```

**通过随机模块制作随机验证码**

```
def v_code():
    checkcode = ''  # 定义一个空字符串
    for i in range(4): # 遍历四次
        num = random.randint(0,9) # 随机选择0到9
        alf = chr(random.randint(65,90)) # 随机选择chr对应的字母
        add = random.choise([num, alf]) # 基于上面的可迭代对象选择一个
        checkcode += str(add) # 将他们变成字符串,加到空字符串里面
    return checkcode
if __name__ == "__main__":
    print(v_code())
```

### os模块

> 用于提供系统级别的操作

```
os.getcwd()                 获取当前工作目录，即当前python脚本工作的目录路径
os.chdir("dirname")         改变当前脚本工作目录；相当于shell下cd
os.curdir                   返回当前目录: ('.')
os.pardir                   获取当前目录的父目录字符串名：('..')
os.makedirs('dir1/dir2')    可生成多层递归目录
os.removedirs('dirname1')   若目录为空，则删除，并递归到上一级目录，如若也为空，则删除，依此类推
os.mkdir('dirname')         生成单级目录；相当于shell中mkdir dirname
os.rmdir('dirname')         删除单级空目录，若目录不为空则无法删除，报错；相当于shell中rmdir dirname
os.listdir('dirname')       列出指定目录下的所有文件和子目录，包括隐藏文件，并以列表方式打印 ***
os.remove()                 删除一个文件
os.rename("oldname","new")  重命名文件/目录 ***
os.stat('path/filename')    获取文件/目录信息，相关信息的介绍 size 文件大小 atime 上次访问时间 mtime 上次修改时间 ctime 查看创建时间
os.sep                      操作系统特定的路径分隔符，win下为"\\",Linux下为"/"
os.linesep                  当前平台使用的行终止符，win下为"\t\n",Linux下为"\n"
os.pathsep                  用于分割文件路径的字符串
os.name                     字符串指示当前使用平台。win->'nt'; Linux->'posix'
os.system("bash command")   运行shell命令，直接显示
os.environ                  获取系统环境变量
os.path.abspath(path)       返回path规范化的绝对路径
os.path.split(path)         将path分割成目录和文件名二元组返回
os.path.dirname(path)       返回path的目录。其实就是os.path.split(path)的第一个元素
os.path.basename(path)      返回path最后的文件名。如何path以／或\结尾，那么就会返回空值。即os.path.split(path)的第二个元素
os.path.exists(path)        如果path存在，返回True；如果path不存在，返回False
os.path.isabs(path)         如果path是绝对路径，返回True
os.path.isfile(path)        如果path是一个存在的文件，返回True。否则返回False
os.path.isdir(path)         如果path是一个存在的目录，则返回True。否则返回False
os.path.join(path1[, path2[, ...]])  将多个路径组合后返回，第一个绝对路径之前的参数将被忽略，涉及文件路径拼接就用它
os.path.getatime(path)      返回path所指向的文件或者目录的最后存取时间
os.path.getmtime(path)      返回path所指向的文件或者目录的最后修改时间
```

### sys

> 提供对Python解释器相关的操作

```
sys.argv           命令行参数List，第一个元素是程序本身路径  *****
sys.exit(n)        退出程序，正常退出时exit(0)
sys.version        获取Python解释程序的版本信息
sys.maxint         最大的Int值
sys.path           返回模块的搜索路径，初始化时使用PYTHONPATH环境变量的值
sys.platform       返回操作系统平台名称
sys.stdin          输入相关
sys.stdout         输出相关
sys.stderror       错误相关
```

**简单的进度条**

```
    import sys
import time
def view_bar(num, total):
    rate = float(num) / float(total)
    rate_num = int(rate * 100)
    r = '\r%d%%' % (rate_num, )
    sys.stdout.write(r)
    sys.stdout.flush()
if __name__ == '__main__':
    for i in range(0, 101):
        time.sleep(0.1)
        view_bar(i, 100)
# 未研究

import sys
import time
for i in range(10):
    sys.stdout.write("#")
    sys.stdout.flush()
    time.sleep(1)
```

### 序列化模块

> 这两个也都是用于序列化的模块, 古老的程序员都用json
>
> `json`: 用于【字符串】和 【python基本数据类型】 间进行转换,
> (数据交互)，可以进行多种语言的数据交互
>
> `pickle`: 用于【python特有的类型】 和 【python基本数据类型】间进行转换　

**json用法**
import json

1. 字典 -> 字符串 系列化

   ```
    dic = {"a":"模特", "b":"萝莉", "c": "小清新"}
    json.dumps(dic, ensure_ascii=False)
   ```

2. 字符串 -> 字典 反序列化

   ```
    str = {"a":"模特", "b":"萝莉", "c": "小清新"}
    json .loads(str)
   ```

3. 把序列化的结果写入到文件中

   ```
    dic = {"a":"模特", "b":"萝莉", "c": "小清新"}
    f = open("test.json", mode="w", encoding="utf-8")
    json.dump(dic, f, ensure_ascii=False) # 把对象打散成json写入到文件中
    f.close()
   ```

4. 从文件件中读取一个json
   f = open("test.json", mode="w", encoding="utf-8")
   dic = json.load(f)
   f.close()

**pickle用法**

```
import pickle                # 和json的方法的相同,序列化对象
dic = {"name":"alex"}
# data = pickle.dumps(dic)   # 转化为字节类型
# print(data)                # ---><class 'bytes'>

f = open("hello","wb")
f.write(pickle.dumps(dic))   #
f.close()

f = open("hello","rb")      # 反序列化
data = pickle.loads(f.read())# 相当于data = pickle.load(f)
print(data)
f.close()
```

**shelve**

shelve模块比pickle模块简单，只有一个open函数，返回类似字典的对象，可读可写;key必须为字符串，而值可以是python所支持的数据类型，会生成三个文件

```
import shelve
  
f = shelve.open(r'shelve.txt')
  
# f['stu1_info']={'name':'alex','age':'18'}
# f['stu2_info']={'name':'alvin','age':'20'}
# f['school_info']={'website':'oldboyedu.com','city':'beijing'}
#
#
# f.close()
  
print(f.get('stu_info')['age'])k
```

### hashlib

用于加密相关的操作，代替了md5模块和sha模块，主要提供 SHA1, SHA224, SHA256, SHA384, SHA512 ，MD5 算法

```
import hashlib
 
hash = hashlib.md5(bytes("bingdu",encoding="utf8")) # 创建md5对象,并额外加密
hash.update(bytes("123",encoding="utf8")) # 对字符串加密
print(hash.hexdigest()) # 取到密文
```

### subprocess

可以执行shell命令的相关模块和函数有

- os.system
- os.spawn*
- os.popen* —废弃
- popen2.*　　—废弃
- commands.* —废弃,3.x中被移除

以上执行shell命令的相关的模块和函数的功能均在 subprocess 模块中实现，并提供了更丰富的功能。

**subprocess.Popen(...)**

用于执行复杂的系统命令

参数：

- args：shell命令，可以是字符串或者序列类型（如：list，元组）

- bufsize：指定缓冲。0 无缓冲,1 行缓冲,其他 缓冲区大小,负值 系统缓冲

- stdin, stdout, stderr：分别表示程序的标准输入、输出、错误句柄

- preexec_fn：只在Unix平台下有效，用于指定一个可执行对象（callable object），它将在子进程运行之前被调用

- close_sfs：在windows平台下，如果close_fds被设置为True，则新创建的子进程将不会继承父进程的输入、输出、错误管道。
  所以不能将close_fds设置为True同时重定向子进程的标准输入、输出与错误(stdin, stdout, stderr)。

- shell：同上

- cwd：用于设置子进程的当前目录

- env：用于指定子进程的环境变量。如果env = None，子进程的环境变量将从父进程中继承。

- universal_newlines：不同系统的换行符不同，True -> 同意使用 \n

- startupinfo与createionflags只在windows下有效
  将被传递给底层的CreateProcess()函数，用于设置子进程的一些属性，如：主窗口的外观，进程的优先级等等

- shell=True: 指定的命令行会通过shell来执行

- stdin : 标准输入

- stdout : 标准输出

- stderr : 标准错误的文件句柄

- PIPE : 管道 ,默认值 为: None, 表示不做重定向，管道可以用来接收数据。

  ```
    把标准输出放入管道中，屏幕上就不会输出内容。
    res=subprocess.Popen("dir", shell=True,stdout=subprocess.PIPE,stdin=subprocess.PIPE,stderr=subprocess.PIPE)   #执行dir命令，交给shell解释器执行，通过标准类型和subprocess.PIPE放入管道中。
  
    >>> res.stdout.read()  #读取管道里面的数据，在程序中，读取也不会输出到屏幕上。
  
    >>> res.stdout.read()   #再read一次，内容就为空，说明读取完成.
    b''  #显示为：bytes类型
  ```

### optparse

Python 有两个内建的模块用于处理命令行参数：

一个是 getopt，《Deep in python》一书中也有提到，只能简单处理 命令行参数；

另一个是 optparse，它功能强大，而且易于使用，可以方便地生成标准的、符合Unix/Posix 规范的命令行说明。

简单流程

首先必须导入模块optparse(这个不多说)

```
import optparse
# 创建OptionParser类对象
parser = optparse.OptionParser()
# 然后,使用add_option来定义命令行参数(伪代码)
# parser.add_option(opt_str, ...
#                   attr= value,...)
# 每个命令行参数就是由参数名字符串和参数属性组成的。如 -f 或者 –file 分别是长短参数名：
parser.add_option("-f","--file",dest = "filename")
# 最后，一旦你已经定义好了所有的命令行参数，调用 parse_args() 来解析程序的命令行：
options,args = parser.parse_args()

注： 你也可以传递一个命令行参数列表到 parse_args()；否则，默认使用 sys.argv[:1]。
parse_args() 返回的两个值：
options，它是一个对象（optpars.Values），保存有命令行参数值。只要知道命令行参数名，如 file，就可以访问其对应的值： options.file 。
args，它是一个由 positional arguments 组成的列表。
```

### struct

学习到socket网络编程这里,对struct有了认识,现在对它进行一些阐释,也可以较为有效的解决粘包问题

struct模块作用：解决bytes和其他二进制数据类型的转换

示例用法：
struct.pack('i',12)

参数说明：

pack函数作用：把任意数据类型变成bytes

i 表示4字节无符号整数。

```
import struct
struct.pack('i',12) # 用方法pack进行打包,把后面的整形数据，封装成一个bytes类型
b'\x0c\x00\x00\x00' # 长度就是4

l=struct.pack('i',12313123)
len(l)
4 #长度就是4
```

**unpack**

```
# 反解
struct.unpack('i',l)
(12313123,)

# 查看类型
l=struct.pack('i',1)
type(l)
<class 'bytes'>  # bytes类型
```

# Python - 模块(二)

## re

re模块提供了正则表达式的相关操作

主要字符介绍:

`.` 通配符,除了换行符之外的任意字符

`^` 匹配字符串的开始

`$` 匹配字符串的结尾

关于次数的:

`*` 按紧挨着的字符重复无数次, 重复零次或更多次

`+` 1到无穷次, 重复一次或更多次

`?` (0,1) 重复零次或一次

`{n}` 重复n次
`{n,}` 重复n次或更多次
`{n,m}` 重复n到m次

其他的:
`[]` 字符集
`|` 或
`()` 分组
`\` 转义

`\A` 只在字符串开始进行匹配

`\Z` 只在字符串结尾进行匹配

`\b` 匹配一个特殊字符边界, 如空格$

`\B` 匹配不位于开始或结尾的空字符串

`\d` 相当于[0-9]

`\D` 相当于[^0-9]

`\s` 匹配任意空字符串:[\t\n\r\v]

`\S` 匹配任意非空白字符:[^\t\n\r\v]

`\w` 匹配任意数字和字母: [a-zA-Z0-9]

`\W` 匹配任意非数字和字母: [^a-zA-Z0-9]

```
. ^ $ * + ? {} 一些用法
import re
print(re.findall("a..x","helloalex"))#['alex']  点匹配任意一个，但是只能匹配一个
 
print(re.findall("^a..x","alexhelloworld"))#['alex'] ^ 匹配以什么开始
 
print(re.findall("a..x$","helloalex"))#['alex'] $ 匹配以什么结尾
 
print(re.findall("alex*","helloalexxxxx"))#['alexxxxx'] * 贪婪匹配 *表示匹配0次或更多次
 
print(re.findall("alex+","helloalexxx"))#['alexxx'] +表示匹配1次或更多次
 
print(re.findall("alex?","helloalexxxx"))#['alex'] ? 表示匹配0到1次
 
print(re.findall("alex{1,2}","alexxxx"))#['alexx']
```

注意：前面的*,+,?等都是贪婪匹配，也就是尽可能匹配，后面加?号使其变成惰性匹配

元字符之字符集［］

```
存在或的关系    至少匹配一个   在字符集中含有特殊意义的只有三个 ^(非) \(转义) -(范围)
 
print(re.findall('a[bc]d','acd'))#['acd']  []字符集中有或的概念
 
print(re.findall('[a-z]','acd'))#['a', 'c', 'd'] #范围a—z 都可以匹配到
 
print(re.findall('[.*+]','a.cd+'))# ['.', '+']
 
print(re.findall('[1-9]','45dha3'))# ['4', '5', '3']
 
print(re.findall('[^ab]','45bdha3'))# ['4', '5', 'd', 'h', '3']   ^ 代表非的概念
 
print(re.findall('[\d]','45bdha3'))# ['4', '5', '3']  \d 表示0到9的数字
```

关于转义的实例 
import re
ret=re.findall('c\l','abc\le')
print(ret) # 报错
ret=re.findall('c\l','abc\le')
print(ret) # 报错
ret=re.findall('c\\l','abc\le')
print(ret) #['c\l']
ret=re.findall(r'c\l','abc\le')
print(ret)#['c\l'] # 一张图解释为什么要用到这么多  的原因

分组() 的用法

```
print(re.findall(r'(ad)+', 'addad')) # ['ad', 'ad']
 
ret=re.search('(?P<id>\d{2})/(?P<name>\w{3})','23/com')
print(ret.group())#23/com
print(ret.group('id'))#23
```

| 或

```
ret=re.search('(ab)|\d','rabhdg8sd')
print(ret.group())#ab    先匹配到哪个取哪个  group表示取值
```

### re下面的方法

**comoile**

```
obj=re.compile('\d{5}')       # compile是编译的意思，编译好一个规则，再进行调用它
ret=obj.search('abc12345ee')
print(ret.group())#12345
```

**serch**

```
print(re.search('al','alvin yuan').group()) # al
 
# 函数会在字符串内查找模式匹配,只到找到第一个匹配然后返回一个包含匹配信息的对象,该对象可以
# 通过调用group()方法得到匹配的字符串,如果字符串没有匹配，则返回None。
```

**findall**

```
print(re.findall('a','alvin yuan'))# ['a', 'a']
 
# 返回所有满足匹配条件的结果,放在列表里
```

**match**

```
ret = re.match('a','abc').group() # match只是从开始匹配，匹配成功则返回对象
print(ret)
```

**split**

```
ret=re.split('[ab]','abcd')     # 先按'a'分割得到''和'bcd',在对''和'bcd'分别按'b'分割
print(ret)#['', '', 'cd']       # 结果放在列表中
```

**sub(subn)**

```
ret=re.sub('\d','abc','alvin5yuan6',1)   # sub里面至少有个三个参数 依次是 匹配规则 替换内容 源数据     也可以有四个，最后一个参数是匹配次数
print(ret)#alvinabcyuan6
ret=re.subn('\d','abc','alvin5yuan6')    # 显示匹配了多少次，替换
print(ret)#('alvinabcyuanabc', 2)
```

**finditer**

```
ret=re.finditer('\d','ds3sy4784a')
print(ret)        #<callable_iterator object at 0x10195f940>
print(next(ret).group())
print(next(ret).group())
 
# 结合迭代器规则，返回的是一个迭代器对象，应用于处理很多的数据，用next的可以逐一取
```

**?: 是取消优先级 ?P的含义**

```
print(re.search("(?P<name>[a-z]+)(?P<age>\d+)","alex36wusir27").group("name"))
# alex 注释 ?P是定义死的 <定义分组名>                                 后面可根据分组名取值

print(re.findall("www\.(baidu|taobao)\.com","sdaswww.baidu.comsdf"))
# ['baidu'] 优先给分组的内容，其实已经匹配到了
print(re.findall("www\.(?:baidu|taobao)\.com","sdaswww.baidu.comsdf"))
# ['www.baidu.com']    ?: 表示去掉括号内的优先级
```

## logging

> 用于便捷记录日志且线程安全的模块
>
> 日志级别大小关系为：CRITICAL ERROR WARNING INFO DEBUG NOTSET，当然也可以自己定义日志级别。
>
> level=logging.DEBUG 设置日志级别

```
import logging
 
logging.basicConfig(filename='log.log',
                    format='%(asctime)s - %(name)s - %(levelname)s -%(module)s:  %(message)s',
                    datefmt='%Y-%m-%d %H:%M:%S %p',
                    level=logging.DEBUG)
 
logging.debug('debug')            # 分五个等级
logging.info('info')
logging.warning('warning')
logging.error('error')
logging.critical('critical')
logging.log(10,'log')
```

文件输出如下:
![img](https://images2015.cnblogs.com/blog/1060991/201612/1060991-20161217162217808-866404352.png)

format 设置输出格式

```
 %(levelno)s: 打印日志级别的数值
 %(levelname)s: 打印日志级别名称
 %(pathname)s: 打印当前执行程序的路径，其实就是sys.argv[0]
 %(filename)s: 打印当前执行程序名
 %(funcName)s: 打印日志的当前函数
 %(lineno)d: 打印日志的当前行号
 %(asctime)s: 打印日志的时间
 %(thread)d: 打印线程ID
 %(threadName)s: 打印线程名称
 %(process)d: 打印进程ID
 %(message)s: 打印日志信息
```

datefmt 设置日期格式，同 time.strftime()

```
  %Y 年 %m 月  %D日  %H时  %M分  %S 秒
```

filename 设置文件路径

filemode 设置文件打开模式

注：没有filename和filemode直接输出

## os

用于提供系统级别的操作

```
os.getcwd()                 获取当前工作目录，即当前python脚本工作的目录路径
os.chdir("dirname")         改变当前脚本工作目录；相当于shell下cd
os.curdir                   返回当前目录: ('.')
os.pardir                   获取当前目录的父目录字符串名：('..')
os.makedirs('dir1/dir2')    可生成多层递归目录
os.removedirs('dirname1')   若目录为空，则删除，并递归到上一级目录，如若也为空，则删除，依此类推
os.mkdir('dirname')         生成单级目录；相当于shell中mkdir dirname
os.rmdir('dirname')         删除单级空目录，若目录不为空则无法删除，报错；相当于shell中rmdir dirname
os.listdir('dirname')       列出指定目录下的所有文件和子目录，包括隐藏文件，并以列表方式打印 ***
os.remove()                 删除一个文件
os.rename("oldname","new")  重命名文件/目录 ***
os.stat('path/filename')    获取文件/目录信息，相关信息的介绍 size 文件大小 atime 上次访问时间 mtime 上次修改时间 ctime 查看创建时间
os.sep                      操作系统特定的路径分隔符，win下为"\\",Linux下为"/"
os.linesep                  当前平台使用的行终止符，win下为"\t\n",Linux下为"\n"
os.pathsep                  用于分割文件路径的字符串
os.name                     字符串指示当前使用平台。win->'nt'; Linux->'posix'
os.system("bash command")   运行shell命令，直接显示
os.environ                  获取系统环境变量
os.path.abspath(path)       返回path规范化的绝对路径
os.path.split(path)         将path分割成目录和文件名二元组返回
os.path.dirname(path)       返回path的目录。其实就是os.path.split(path)的第一个元素
os.path.basename(path)      返回path最后的文件名。如何path以／或\结尾，那么就会返回空值。即os.path.split(path)的第二个元素
os.path.exists(path)        如果path存在，返回True；如果path不存在，返回False
os.path.isabs(path)         如果path是绝对路径，返回True
os.path.isfile(path)        如果path是一个存在的文件，返回True。否则返回False
os.path.isdir(path)         如果path是一个存在的目录，则返回True。否则返回False
os.path.join(path1[, path2[, ...]])  将多个路径组合后返回，第一个绝对路径之前的参数将被忽略，涉及文件路径拼接就用它
os.path.getatime(path)      返回path所指向的文件或者目录的最后存取时间
os.path.getmtime(path)      返回path所指向的文件或者目录的最后修改时间
```

![img](https://images2015.cnblogs.com/blog/1060991/201612/1060991-20161222001322292-1975980018.png)

上图所示, 是得到:

```
os.path.dirname是返回上一级目录
```

**获取文件大小的三种方式**

```
import os

res = os.path.getsize(os.path.join("F:\python\PycharmProjects\date2017.1.8","path_search.py"))
print(res)

ret  = os.stat(os.path.join("F:\python\PycharmProjects\date2017.1.8","path_search.py")).st_size
print(ret)

f = open(os.path.join("F:\python\PycharmProjects\date2017.1.8","path_search.py"),"rb")
print(len(f.read()))

输出结果相同
```

**os模块的补充**

```
os.walk(top, topdown=True, onerror=None, followlinks=False) 
可以得到一个三元tupple(dirpath, dirnames, filenames), 
第一个为起始路径，第二个为起始路径下的文件夹，第三个是起始路径下的文件。
dirpath 是一个string，代表目录的路径，
dirnames 是一个list，包含了dirpath下所有子目录的名字。
filenames 是一个list，包含了非目录文件的名字。
这些名字不包含路径信息，如果需要得到全路径，需要使用os.path.join(dirpath, name).
通过for循环自动完成递归枚举

# 做个简单的例子，输出crm目录的下所有文件的路径信息
for a,b,c in os.walk(str(PATH) + os.sep + "crm" ):
    for item in c:
        # print(item)
        print(os.path.join(a,item))

输出
F:\python\crm\.idea\.name
F:\python\crm\.idea\crm.iml
F:\python\crm\.idea\encodings.xml
F:\python\crm\.idea\misc.xml
F:\python\crm\.idea\modules.xml
F:\python\crm\.idea\workspace.xml
F:\python\crm\bin\bin.py
F:\python\crm\bin\__init__.py
F:\python\crm\bin\__pycache__\bin.cpython-35.pyc
F:\python\crm\core\admin.py
F:\python\crm\core\coures.py
F:\python\crm\core\grade.py
F:\python\crm\core\school.py
F:\python\crm\core\student.py
F:\python\crm\core\teacher.py
F:\python\crm\core\__init__.py
F:\python\crm\core\__pycache__\admin.cpython-35.pyc
F:\python\crm\core\__pycache__\coures.cpython-35.pyc
F:\python\crm\core\__pycache__\grade.cpython-35.pyc
F:\python\crm\core\__pycache__\school.cpython-35.pyc
F:\python\crm\core\__pycache__\student.cpython-35.pyc
F:\python\crm\core\__pycache__\teacher.cpython-35.pyc
F:\python\crm\core\__pycache__\__init__.cpython-35.pyc
F:\python\crm\db\admin\alex
F:\python\crm\db\coures\23eeeb4347bdd26bfc6b7ee9a3b755dd
F:\python\crm\db\coures\34d1f91fb2e514b8576fab1a75a89a6b
F:\python\crm\db\coures\c71e8d17d41c21de0d260881d69662ff
F:\python\crm\db\coures\df5fb5e33c5585bb0c48107c57cece9b
F:\python\crm\db\coures\e206a54e97690cce50cc872dd70ee896
F:\python\crm\db\grade\270c42ba7614f1a475f61dfcb397a621
F:\python\crm\db\grade\817ee0b8010393ff3b4483e703663551
F:\python\crm\db\school\17811d3caeff9648f48b5a553c806c63
F:\python\crm\db\school\b035c88ee6f5270ccff67a591d0e21ec
F:\python\crm\db\school\bb0ac3d8eb8f2c2f6fe336c5e9957392
F:\python\crm\db\school\e523d5f211747bdfc742f50463577f74
F:\python\crm\db\student\6e7e12c264fb3e1f456b0782f47e4af6
F:\python\crm\db\student\a0b5e2d3a97d7a19ec6d2da830f609b2
F:\python\crm\db\student\a995b03ed63f8c7128a83c984b89aa50
F:\python\crm\db\student\fe01b2ba5ec0146e5d4b0885822556ef
F:\python\crm\db\student\ff4916088e836d268a9d72f8929bac06
F:\python\crm\db\teacher\08319d4fed47c8ed828ebabd5a91563b
F:\python\crm\db\teacher\0d1d5cd3623144f692fb771580b92288
F:\python\crm\db\teacher\5d00d0762936aedda519d63c2e4a2a40
F:\python\crm\db\teacher\6a7ece82e4ed94a475dab275891d5036
F:\python\crm\lib\ceshi.py
F:\python\crm\lib\readme
F:\python\crm\lib\readwrite.py
F:\python\crm\lib\__init__.py
F:\python\crm\lib\__pycache__\readwrite.cpython-35.pyc
F:\python\crm\lib\__pycache__\__init__.cpython-35.pyc
```