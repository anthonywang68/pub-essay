---
title: pandas笔记，持续学习更新。。。。
date: 2019-11-23 14:21:58
tags:
---

 参考：Pandas 支持很多种文件格式的输入输出, 具体可以参考下官方文档 https://pandas.pydata.org/pandas-docs/version/0.22/io.html 

<!--more-->

首先, 准备一个 excel 文件, 大致内容如下, 并保存成 .csv 格式.
![img](https://img2018.cnblogs.com/blog/1603147/201902/1603147-20190219172617115-289882360.jpg)

然后, 在 jupyter notebook 里执行如下代码:
``

```python
#引入 pandas 模型
import pandas as pd
# 读取 csv 文件
df = pd.read_csv('weather_data.csv')
# 打印
df
```

``
在 jupyter notebook 里的表现形式大概如下:
![img](https://img2018.cnblogs.com/blog/1603147/201902/1603147-20190219172645482-2146952103.jpg)
就这么简单, 我们就把一个 csv 文件转换成 dataframe 格式了. 这里大家在操作中可能遇到的一个报错就是找不到文件. 这是路径问题, 解决方法很简单, 打开你运行 jupyter 的终端窗口, 找到如下路径, 把你的 csv 文件丢进去就可以啦. 
![img](https://img2018.cnblogs.com/blog/1603147/201902/1603147-20190219172658084-582961805.jpg)

OK, 上面介绍了如何将外部文件转换成 dataframe. 下面介绍从 python dictionary 转换成 dataframe:

```python
# python dictionary
weather_data = {
    'day': ['1/1/2017', '1/2/2017', '1/3/2017', '1/4/2017', '1/5/2017', '1/6/2017'], 
    'temperature': [32, 35, 27, 25, 24, 30],
    'windspeed': [6, 7, 2, 5, 4, 6],
    'event': ['Rain', 'Sunny', 'Snow', 'Snow', 'Rain', 'Sunny']
}
# 转换
df = pd.DataFrame(weather_data)
打印
df
```

运行结果:
![img](https://img2018.cnblogs.com/blog/1603147/201902/1603147-20190219172712714-604237626.jpg)

可以说, 两种方式都非常的简便, 下面就基于上面的数据看一下 dataframe 有哪些属性可以供我们使用.

```python
df.shape
```

输出:
(6, 4)
这里是查看这个 dataframe 的行数和列数, 显然, 我们这个例子中, 有 6 行, 4 列. 那么我们还可以把这个结果同时赋值给两个变量, 再分别查看这两个变量的值:

```python
rows, columns = df.shape
rows
```

输出:
6

```python
columns
```

输出:
4

```python
# 查看前5行数据
df.head()
# 查看后5行数据
df.tail()
# 查看后 3 行数据
df.tail(3)
# 查看 第 2,3,4 行的数据
df[2:5]
# 查看所有数据
df[:]
# 查看所有列
df.columns
# 查看某一列方法一, 只适用于列名中间没有空格的
df.day
# 查看某一列方法二, 适用于所有列名
df['event']
# 查看某一列的类型, 这里输出的结果是 pandas.core.series.Series, 表示每一列都是一个 series
type(df['event'])
# 查看两个列以上的数据, 注意这里要用两个中括号
df[['event', 'day']]
```

以上就是 dataframe 的一些基本属性. 下面介绍一些操作命令

```python
# 求某一列里的最大值
df['temperature'].max()
# 求某一列的平均值
df.temperature.mean()
# 查看 temperature 大于 30 的数据
df[df.temperature>30]
# 查看 temperature 等于最大值的数据
df[df.temperature==df.temperature.max()]
# 只查看 temperature 等于最大值的日期, 有下面两种写法
df['day'][df.temperature==df.temperature.max()]
df.day[df.temperature==df.temperature.max()]
# 查看 temperature 等于最大值的日期和温度
df[['day', 'temperature']][df.temperature==df.temperature.max()]
# 查看目前的索引
df.index
# 设置索引, 这里注意必须加上第二个参数, 以确保真正更改到 df 的索引
df.set_index('day', inplace=True)
# 基于上面把 'day' 设为索引, 就可以具体查看某一行的数据
df.loc['1/4/2017']
# 重置索引
df.reset_index(inplace=True)
# 把索引设置为 'event', 这里要说明两个问题, 第一, 更新索引必须在重置索引的前提下, 否则 'day'列就消失了, 第二, 任何列都可以被设置为索引
df.set_index('event', inplace=True)
```

最后再介绍一个命令, 这里就是会输出所有数字内容的列, 并且罗列出一些基本常用的运算结果.
![img](https://img2018.cnblogs.com/blog/1603147/201902/1603147-20190219172729484-1446928958.jpg)

 生成 Dataframe 的几种方式:

1. CSV
2. Excel
3. python dictionary
4. List of tuples
5. List of dictionary

下面分别一一介绍具体的实现方式:

- 通过 csv 文件
  这里补充一个知识点, 就是如果要读取的文件不在 jupyter 所在的文件夹, 则可以通过绝对路径的方式引入.

```python
df = pd.read_csv("/Users/rachel/Downloads/weather.csv")
```

- 通过 Excel 文件
  这里的第二个参数是必填项, 因为要指明具体读取 excel 表中的哪个 sheet.

```python
df = pd.read_excel("/Users/rachel/Downloads/weather.xlsx", "weather")
```

还有一个小坑, 就是在初次运行的时候有可能会提示错误, 根据错误提示, 大概可以了解到, 要读取 excel 文件, 还需要一个 xlrd 的包, 在终端运行下面命令就好了

```text
pip3 install xlrd
```

- 通过 python dictionary (为了方便大家日后可以更好地理解英文文档, 这里的一些专业名词, 我就都不翻译了)

```python
weather_data = {
    'day': ['1/1/2017','1/2/2017','1/3/2017'],
    'temperature': [32,35,28],
    'windspeed': [6,7,2],
    'event': ['Rain', 'Sunny', 'Snow']
}
df = pd.DataFrame(weather_data)
```

- 通过 List of tuples

```python
weather_data = [
    ('1/1/2017',32,6,'Rain'),
    ('1/2/2017',35,7,'Sunny'),
    ('1/3/2017',28,2,'Snow')
]
df = pd.DataFrame(data=weather_data, columns=['day','temperature','windspeed','event'])
```

上面例子中, weather_data 的数据结构是一个 list(特点是中括号), list 中的每一个元素就是一个 tuple, 由于原数据没有指明列名, 所以在创建 dataframe 的时候, 需要指明列名.

- 通过 List of dictionary, 从名字就可以读出来下面的数据结构是一个 list, list 中的每个元素又是一个 dictionary.

```python
weather_data = [
    {'day': '1/1/2017', 'temperature': 32, 'windspeed': 6, 'event': 'Rain'},
    {'day': '1/2/2017', 'temperature': 35, 'windspeed': 7, 'event': 'Sunny'},
    {'day': '1/3/2017', 'temperature': 28, 'windspeed': 2, 'event': 'Snow'},
    
]
df = pd.DataFrame(data=weather_data, columns=['day','temperature','windspeed','event'])
```

上面简要介绍了 5 中生成 dataframe 的方式, 其实 Pandas 还支持很多种文件格式的输入输出, 具体可以参考下官方文档 https://pandas.pydata.org/pandas-docs/version/0.22/io.html