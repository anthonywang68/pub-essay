---
title: python
date: 
tags:
---

# Python 基本数据类型

<!--more-->

## 一. 数字 (int)

### 基本介绍

> python 的数字类型基本分为 整数,长整数, 浮点数.
>
> 整数: 类如 1, 11, 22, 36...
>
> 长整数: 就是比较长的整数,本身还是整数,
> 哈哈~ 类如 154864131, 548798466111131313...
>
> 浮点数: 简单理解为带小数点的数, 类如 3.14, 0.78, 99.000000,9999.8888888...

### 1. 数字的转换

```
`.int(x base=None) `将X转换为一个整数, base为按照多少进制进行转换.

`.float(x)` 将x转换为一个浮点数.

`.complex(x)`将x转换为一个复数,实数部分为x, 虚数部分为0.

`.complex(x, y)`将 x 和 y 转换到一个复数，实数部分为 x，虚数部分为 y。x 和 y 是数字表达式

`.bit_lenght(x)` 当前数字二进制后，至少用几位表示

`bin()`函数将十进制转换为二进制
`oct()`函数将十进制转换为八进制
`hex()`函数将十进制转换为十六进制
```

### 2. 数学函数

```
abs(x)  返回数字的绝对值，如abs(-10) 返回 10

ceil(x) 返回数字的上入整数，如math.ceil(4.1) 返回 5

cmp(x, y)如果 x < y 返回 -1, 如果 x == y 返回 0, 如果 x > y 返回 1。 Python 3 已废弃 。使用 使用 (x>y)-(x<y) 替换。

exp(x)  返回e的x次幂(ex),如math.exp(1) 返回2.718281828459045

fabs(x) 返回数字的绝对值，如math.fabs(-10) 返回10.0

floor(x)    返回数字的下舍整数，如math.floor(4.9)返回 4

log(x)  如math.log(math.e)返回1.0,math.log(100,10)返回2.0

log10(x)    返回以10为基数的x的对数，如math.log10(100)返回 2.0

max(x1, x2,...) 返回给定参数的最大值，参数可以为序列。

min(x1, x2,...) 返回给定参数的最小值，参数可以为序列。

modf(x) 返回x的整数部分与小数部分，两部分的数值符号与x相同，整数部分以浮点型表示。

pow(x, y)   x**y 运算后的值。

round(x [,n])   返回浮点数x的四舍五入值，如给出n值，则代表舍入到小数点后的位数。

sqrt(x) 返回数字x的平方根，数字可以为负数，返回类型为实数，如math.sqrt(4)返回 2+0j
```

### 3. 三角函数

```
acos(x) 返回x的反余弦弧度值。

asin(x) 返回x的反正弦弧度值。 

atan(x) 返回x的反正切弧度值。

atan2(y, x) 返回给定的 X 及 Y 坐标值的反正切值。

cos(x)  返回x的弧度的余弦值。

hypot(x, y) 返回欧几里德范数 sqrt(x*x + y*y)。

sin(x)  返回的x弧度的正弦值。

tan(x)  返回x弧度的正切值。

degrees(x)  将弧度转换为角度,如degrees(math.pi/2) ， 返回90.0

radians(x)  将角度转换为弧度
```

### 4. 数学常量

```
pi  数学常量 pi（圆周率，一般以π来表示）

e   数学常量 e，e即自然常数（自然常数）。
```

## 二. 字符串 (str)

定义：它是一个有序的字符的集合，用于存储和表示基本的文本信息，‘’或“”或‘’‘ ’‘’中间包含的内容称之为字符串

　　　字符串的结构类型为'...'　　"..."　　"'..."'

　　　字符串一旦创建，则不可以修改

　　　一旦修改或者拼接，都会造成重新生成字符串，则要赋予一个新的值

> 1. 索引从0开始
> 2. len获取当前字符串中有几个字符组成（计算长度）
> 3. 切片，类似于区间 例如[0:4]则表示0、1、2、3 后面是不包含关系
> 4. for循环　　用法 for　变量名（每个字符）　in　字符串
> 5. `range`　　　帮助创建连续的数字，通过设置步长来指定不连续
>    在python2中，range(0,100) 会直接输出0到99的数字
>    而在python3中，range(0,100)不会直接输出数字，还是原样，则此时需要调用for循环进行输出，省内存

### Str的方法:

```
capitalize()
将字符串的第一个字符转换为大写

center(width, fillchar) 
返回一个指定的宽度 width 居中的字符串，fillchar 为填充的字符，默认为空格。

count(str, beg= 0,end=len(string))
返回 str 在 string 里面出现的次数，如果 beg 或者 end 指定则返回指定范围内 str 出现的次数
    
bytes.decode(encoding="utf-8", errors="strict") 
Python3 中没有 decode 方法，但我们可以使用 bytes 对象的 decode() 方法来解码给定的 bytes 对象，这个 bytes 对象可以由 str.encode() 来编码返回。
    
encode(encoding='UTF-8',errors='strict')
以 encoding 指定的编码格式编码字符串，如果出错默认报一个ValueError 的异常，除非 errors 指定的是'ignore'或者'replace'
    
endswith(suffix, beg=0, end=len(string))
检查字符串是否以 obj 结束，如果beg 或者 end 指定则检查指定的范围内是否以 obj 结束，如果是，返回 True,否则返回 False.
    
expandtabs(tabsize=8)
把字符串 string 中的 tab 符号转为空格，tab 符号默认的空格数是 8 。
    
find(str, beg=0 end=len(string))
检测 str 是否包含在字符串中 中，如果 beg 和 end 指定范围，则检查是否包含在指定范围内，如果是返回开始的索引值，否则返回-1
    
index(str, beg=0, end=len(string))
跟find()方法一样，只不过如果str不在字符串中会报一个异常.
    
isalnum()   
如果字符串至少有一个字符并且所有字符都是字母或数字则返 回 True,否则返回 False
    
isalpha()
如果字符串至少有一个字符并且所有字符都是字母则返回 True, 否则返回 False
    
isdigit()   
如果字符串只包含数字则返回 True 否则返回 False..
    
islower()
如果字符串中包含至少一个区分大小写的字符，并且所有这些(区分大小写的)字符都是小写，则返回 True，否则返回 False
    
isnumeric()
如果字符串中只包含数字字符，则返回 True，否则返回 False
    
isspace()
如果字符串中只包含空格，则返回 True，否则返回 False.
    
istitle()
如果字符串是标题化的(见 title())则返回 True，否则返回 False
    
isupper()
如果字符串中包含至少一个区分大小写的字符，并且所有这些(区分大小写的)字符都是大写，则返回 True，否则返回 False
    
join(seq)
以指定字符串作为分隔符，将 seq 中所有的元素(的字符串表示)合并为一个新的字符串
    
len(string)
返回字符串长度
    
ljust(width[, fillchar])
返回一个原字符串左对齐,并使用 fillchar 填充至长度 width 的新字符串，fillchar 默认为空格。
    
lower()
转换字符串中所有大写字符为小写.
    
lstrip()
截掉字符串左边的空格
    
maketrans()
创建字符映射的转换表，对于接受两个参数的最简单的调用方式，第一个参数是字符串，表示需要转换的字符，第二个参数也是字符串表示转换的目标。
    
max(str)
返回字符串 str 中最大的字母。
    
min(str)
返回字符串 str 中最小的字母。
    
replace(old, new [, max])
把 将字符串中的 str1 替换成 str2,如果 max 指定，则替换不超过 max 次。
    
rfind(str, beg=0,end=len(string))
类似于 find()函数，不过是从右边开始查找.
    
rindex( str, beg=0, end=len(string))
类似于 index()，不过是从右边开始.

rjust(width,[, fillchar])
返回一个原字符串右对齐,并使用fillchar(默认空格）填充至长度 width 的新字符串
    
rstrip()
删除字符串字符串末尾的空格.
    
split(str="", num=string.count(str))
num=string.count(str)) 以 str 为分隔符截取字符串，如果 num 有指定值，则仅截取 num 个子字符串
    
splitlines([keepends])
按照行('\r', '\r\n', \n')分隔，返回一个包含各行作为元素的列表，如果参数 keepends 为 False，不包含换行符，如果为 True，则保留换行符。
    
startswith(str, beg=0,end=len(string))
检查字符串是否是以 obj 开头，是则返回 True，否则返回 False。如果beg 和 end 指定值，则在指定范围内检查。
    
strip([chars])
在字符串上执行 lstrip()和 rstrip()
    
swapcase()
将字符串中大写转换为小写，小写转换为大写
    
title()
返回"标题化"的字符串,就是说所有单词都是以大写开始，其余字母均为小写(见 istitle())
    
translate(table, deletechars="")
根据 str 给出的表(包含 256 个字符)转换 string 的字符, 要过滤掉的字符放到 deletechars 参数中

upper()
转换字符串中的小写字母为大写
    
zfill (width)
返回长度为 width 的字符串，原字符串右对齐，前面填充0

isdecimal()
检查字符串是否只包含十进制字符，如果是返回 true，否则返回 false。
```

## 三. 列表 (List)[]

如：[11,22,33]、['wupeiqi', 'alex']

### List 的方法

```
需要记忆的魔法有：append（追加），extend（扩展），insert（插入）

append　　原来值最后追加，数字，字符，列表都可以进行追加
    li = [6,8,7,5,8]
    li.append(2)                # 追加再最后 原来值再后面追加，因此不用赋予新的值
    print(li)
    　　  
clear　　   清空列表
    li = [6,8,7,5,8]
    li.clear()                  # 清空列表
    print(li)
    　　  
copy　　　拷贝（浅拷贝）
    li = [6,8,7,5,8]
    v = li.copy()               # 拷贝，浅拷贝
    print(li)

count　　  计数（计算元素出现的次数）
    li = [6,8,7,5,8]
    v = li.count(8)             # 计算元素出现的次数
    print(v)

extend　　扩展原列表，参数可迭代对象（数字不能扩展，数字不可迭代）
    li = [6,8,7,5,8]
    li.extend("b")              # 扩展追加。
    print(li)

index　　　根据值获取值的指引位置
    li = [6,8,7,5,8]
    v = li.index(8)             # 根据值获取值的索引位置
    print(v)<br><br>1　　　　　　　　　　　　　　　  重复字符，获取最左边位置，便不再寻找

insert　　　插入，也可以根据指引位置插入元素
    li = [6,8,7,5,8]
    li.insert(2,"b")　　　　　　　# 前面参数指定索引，逗号分隔，加要插入的元素
    print(li)

pop　　　　可索引删除某个值，无索引则默认删除最后一个值，赋予一个值也可以获得被删除的值
    li = [6,8,7,5,8]
    v = li.pop(2)               # 指定索引删除，无索引则默认删除最后一个值，也可以获取删除的值
    print(li)
    print(v)

remove　　删除列表指定值，左边优先
    li = [6,8,7,5,8]
    li.remove(8)                # 删除列表指定值，从左优先
    print(li)

reverse　　将当前列表进行反转
    li = [6,8,7,5,8]
    li.reverse()                # 将当前列表进行反转
    print(li)

sort　　　　排序，有秩序的排序。
    li = [6,8,7,5,8]
    li.sort(reverse=True)    　　# 当反转正确，则从大到小排序
    print(li)
```

### 列表推导式

> 列表推导式提供了从序列创建列表的简单途径。通常应用程序将一些操作应用于某个序列的每个元素，用其获得的结果作为生成新列表的元素，或者根据确定的判定条件创建子序列。
>
> 每个列表推导式都在 for 之后跟一个表达式，然后有零到多个 for 或 if 子句。返回结果是一个根据表达从其后的 for 和 if 上下文环境中生成出来的列表。如果希望表达式推导出一个元组，就必须使用括号。

```
1 >>> vec1 = [2, 4, 6]
2 >>> vec2 = [4, 3, -9]
3 >>> [x*y for x in vec1 for y in vec2]
4 [8, 6, -18, 16, 12, -36, 24, 18, -54]
5 >>> [x+y for x in vec1 for y in vec2]
6 [6, 5, -7, 8, 7, -5, 10, 9, -3]
7 >>> [vec1[i]*vec2[i] for i in range(len(vec1))]
8 [8, 12, -54]
```

## 四. 元祖 tuple()

> 是对列表的二次加工，书写格式为括号（），里面放元素
>
> 元组的一级元素不可被修改，且不能被增加和删除
>
> 一般写元组的时候，推荐在最后加入逗号, 　　能加则加

### 元祖运算符

与字符串一样，元组之间可以使用 + 号和 * 号进行运算。这就意味着他们可以组合和复制，运算后会生成一个新的元组。

例如:

```
print(len((1, 2, 3,))) 结果为 3

(1, 2, 3) + (4, 5, 6)   结果为 (1, 2, 3, 4, 5, 6)


('Hi!',) * 4    结果为 ('Hi!', 'Hi!', 'Hi!', 'Hi!')


3 in (1, 2, 3)  结果为 True


for x in (1, 2, 3): print(x)    结果为 1 2 3
```

### tuple的方法

```
len(tuple)  计算元祖个数

max(tuple)  返回元祖中元素最大值

min(tuple)  返回元祖中元素最小值

tuple(seq)  将列表转换为元祖
```

## 五. 字典 dict{}

是一个键（key）值（value）对，结构为{}，大括号

> 字典的值可以是任何值
>
> 列表，字典不能作为字典的key
> 字典是无序的
> 字典不能被切片，因为它是无序的；可根据索引取值，查找，找到元素
> 字典是支持del 删除的
> 支持for循环

### 字典的方法

```
1   dict.clear()
删除字典内所有元素

2   dict.copy()
返回一个字典的浅复制

3   dict.fromkeys()
创建一个新字典，以序列seq中元素做字典的键，val为字典所有键对应的初始值
print(dict.fromkeys('2', 4))    结果为 {'2': 4} 注意: 整数不能作为Key

4   dict.get(key, default=None)
返回指定键的值，如果值不在字典中返回default值

5   key in dict
如果键在字典dict里返回true，否则返回false

6   dict.items()
以列表返回可遍历的(键, 值) 元组数组

7   dict.keys()
以列表返回一个字典所有的键

8   dict.setdefault(key, default=None)
和get()类似, 但如果键不存在于字典中，将会添加键并将值设为default

9   dict.update({'k1':'v1'} OR k1=v1)
把字典dict2的键/值对更新到dict里

10  dict.values()
以列表返回字典中的所有值

11  
dict.popitem()
随机删除，并获取删除的键值以元组形式返回
```

## 六. 集合 set

基本数据类型特点（可变：列表，字典 　　不可变：字符串，数字，元组）

> 不同的元素组成
>
> 无序
>
> 集合中的元素必须是不可变类型，加入可变的类型会报错==

```
student = {'Tom', 'Jim', 'Mary', 'Tom', 'Jack', 'Rose'}
 
print(student)   # 输出集合，重复的元素被自动去掉

# 成员测试
if('Rose' in student) :
    print('Rose 在集合中')
else :
    print('Rose 不在集合中')


# set可以进行集合运算
a = set('abracadabra')
b = set('alacazam')
 
print(a)
 
print(a - b)     # a和b的差集
 
print(a | b)     # a和b的并集
 
print(a & b)     # a和b的交集
 
print(a ^ b)     # a和b中不同时存在的元素
```

### set的方法

A、add　　添加，添加可变的数据类型也是会报错的

```
s = {1,2,5,5,'g','h'}
s.add(3)
print(s)
```

执行结果

```
{1, 2, 3, 5, 'h', 'g'}
```

B、clear　 清空集合

C、intersection　　交集，相当于&

```
s = {1,7,9}
s1 = {1,9,5}
print(s&s1)
print(s.intersection(s1))
 
{9, 1}
{9, 1}
```

D、union　　并集，相当于 |

```
s = {1,7,9}
s1 = {1,9,5}
print(s|s1)
print(s.union(s1))<br><br>{1,5,7,9}<br>{1,5,7,9}
```

E、difference　　差集，相当于-

```
s = {1,7,9}
s1 = {1,9,5}
print(s-s1)
print(s.difference(s1))
 
{7}
{7}
```

F、symmetric_difference　　交叉补集，相当于^

```
s = {1,7,9}
s1 = {1,9,5}
print(s^s1)
print(s.symmetric_difference(s1))
 
{5, 7}
{5, 7}
```

G、difference-update　　 差集更新

H、isdisjoint　　　　　　 判断是否有交集

I、issubset　　　　　　　 判断是否为子集

```
s1={1,2}
s2={1,2,3}
print(s1.issubset(s2))
print(s2.issubset(s1))
 
True
False
```

J、issuperset　　　　　　 判断是否为父集

```
s1={1,2}
s2={1,2,3}
print(s2.issuperset(s1))
 
True
```

k、update　　更新多个值，可迭代都可传

L、pop,remove,discard　　均为删除

```
s = {9, 'sb', 1, 2, 3, 4, 5, 6}
s.pop()                           # pop不能指定删除，只可以随机删除
print(s)
s = {9, 'sb', 1, 2, 3, 4, 5, 6}  # 指定删除元素，当删除的元素不存在时，删除会报错
s.remove(4)
print(s)
s = {9, 'sb', 1, 2, 3, 4, 5, 6}
s.discard(5)
print(s)                          # 指定元素删除；和remove唯一的区别删除不存在不会报错
```