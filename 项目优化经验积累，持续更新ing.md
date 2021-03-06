---
title: 项目开发优化经验积累，持续更新ing
date: 2019-09-19 09:02:48
tags:
---



对应开发人员，网站性能优化一般包括Web前端性能优化、应用服务器性能优化、存储服务器性能优化三类。

<!--more-->

1.合理的使用缓存技术，对一些常用但是不会经常变动的页面数据做一个缓存，比如首页，设置一定的过期时间，这样减少了对数据库的访问压力，从而提升网站性能。

　　2.使用celery消息队列。将耗时的任务扔到队列里，让worker去监听队列里的任务，实现异步操作，比如发邮件，发短信等。

　　3.在代码逻辑上的优化。

　　4.搭建服务器集群，将并发量请求分散到多台服务器上处理。

　　5.最后就是运维人员的一些性能优化技术了。

下面详细说：

### 1、Web前端性能优化：

#### 1.1 减少http请求

http协议是无状态的应用层协议，意味着每次http请求都需要建立通信链路、进行数据传输，而在服务器端，每个http请求都需要启动独立的线程去处理。

减少http请求的数目可有效提高访问性能。

减少http请求的主要手段是：**合并CSS、合并JavaScript、合并图片。**

#### 1.2 使用浏览器缓存

对于一个网站而言，CSS、JavaScript、logo、图标。这些静态资源文件更新的频率都比较低，而这些文件几乎是每次http请求都需要的。如果将这些文件缓存在浏览器中，可以极好的改善性能。

通过http头中的**cache-control和expires**的属性，可设定浏览器缓存，缓存时间可以进行自定义。

#### 1.3 启用压缩

在服务器端对文件进行压缩，在浏览器对文件解压缩，可以有效减少通信传输的数据量。如果可以的话，尽可能的将外部的脚本、样式进行合并，多个合并为一个。

文本文件的压缩效率可以达到80%以上。因此HTML、CSS、JavaScript文件启用GZip压缩可达到较好的效果。但是压缩对服务器和浏览器产生一定的压力，在网络宽带良好，而服务器资源不足的情况下要综合考虑。

#### 1.4 CSS放在页面最上部，JavaScript放在页面最下面

浏览器会在下载完成全部CSS之后才对整个页面进行渲染，因此最好的做法是将CSS放在页面最上面，让浏览器尽快下载CSS。

JavaScript则相反，浏览器在加载JavaScript后立即执行，有可能会阻塞整个页面，造成页面显示缓慢，因此JavaScript最后放在页面最下面。

### 2、应用服务器性能优化：

应用服务器也就是处理网站业务的服务器，网站的业务代码都部署在这里，主要优化方案有缓存、异步、集群等。

#### 2.1 合理使用缓存

当网站遇到性能瓶颈时，第一个解决方案一般是缓存。在整个网站应用中，缓存几乎无处不在，无论是客户端，还是应用服务器，或者是数据库服务器。在客户端和服务器的交互中，无论是数据、文件都可以缓存，合理使用缓存对网站性能优化非常重要。

缓存一般用来存放那些读写次数比较高，变化较少的数据，比如网站首页的信息、商品的信息等。应用程序读取数据时，一般是先从缓存中读取，如果读取不到或数据已失效，再访问磁盘数据库，并将数据再次写入缓存。

缓存的基本原理是将数据存储在相对有较高访问速度的存储介质中，比如内存，一方面缓存访问速度快，另一方面，如果缓存的数据是需要经过计算处理得到的，那使用缓存还可以减少服务器处理数据的计算时间。

使用缓存并不是没有缺陷，内存资源是比较宝贵的，不可能将所有的数据都缓存，一般频繁修改的数据不建议使用缓存，这会导致数据不一致。

网站数据缓存一般遵循二八定律，即80%的访问都在20%的数据上，所以，一般讲这20%的数据缓存，可以起到改善系统性能，提高服务器读取效率。

#### 2.2 异步操作

使用消息队列将调用异步化，可以改善网站系统的性能。

在不是有消息队列的情况下，用户的请求直接写入数据库，在高并发的情况下，会对数据库造成非常大的压力，也会延迟响应时间。

在使用消息队列后，用户请求的数据会发送给消息队列服务器，消息队列服务器会开启进程，将数据异步写入到数据库，消息队列服务器的处理速度远超过数据库，因此用户的响应延迟可得到改善。

消息队列可以将短时间内的高并发的事务消息，存储在消息队列中，从而提高网站的高并发处理能力，在电商网站的促销活动中，合理使用消息队列，可以抵御短时间内用户高并发的冲击。

#### 2.3 使用集群

在网站高并发访问的情况下，使用负载均衡技术，可以为一个应用构建又多台服务器组成的服务器集群，将并发访问请求，分发到多台服务器上处理，避免单一服务器因负载过大，而导致响应延迟。

#### 2.4 代码优化

网站的业务逻辑代码主要步数在应用服务器上，需要处理复杂的并发事务。合理优化业务代码，也可以改善网站性能。

任何Web网站度会遇到多用户的并发访问，大型网站的并发用户会达到数万，每个用户请求都会创建一个独立的系统进程去处理。由于线程比进程更轻量，占用资源更少，所以，目前主流的Web应用服务器都是采用多线程的方式，处理并发用户的请求，因此，网站开发多数都是多线程编程。

使用多线程的另一个原因是服务器有多个CPU，现在手机都到了8核CPU的时代，一般的服务器至少是16核的CPU，要想最大限度的使用这些CPU，必须启动多线程。

那么，启动多少线程合适呢？

启动线程数和CPU内核数量成正比，和IO等待时间成正比，如果都是计算型任务，那么线程数最多不要超过CPU内核数，因为启动再多，CPU也来不及调用，如果任务是得带读写磁盘、网络响应，那么多启动线程会提高任务并发度，提高服务器性能。

或者使用下面公式来描述：

**启动线程数 = （任务执行时间 / （任务执行事件 - IO等待时间））\* CPU内核数**

数据的读写是网站处理并发访问的另一个瓶颈。使用缓存虽然可以解决一部分数据读写压力，但很多的时候，磁盘仍然是系统系统最严重的瓶颈。而且磁盘是网站最重要的资产，磁盘的可用性和容错性也是至关重要的。

机械硬盘和固态硬盘： 机械硬盘是目前最常用的硬盘，通过马达带动磁头到指定磁盘的位置访问数据，每次访问数据都需要移动磁头，在读取连续数据和随机访问上，磁头移动的次数相差较大，因此机械硬盘的性能表现差别巨大，读写效率较低。而在网站应用中，大多数数据的访问都是随机的，在这种情况下，固态硬盘具有更高的性能 ，但目前固态硬盘在工艺上、数据可靠性上还有待提升，因此固态硬盘的使用尚未普及，从发展趋势看，取代机械硬盘应该是迟早是事儿。

总结：

网站性能优化是在用户高并发访问，网站遇到问题时的解决方案，所以网站性能优化的主要内容是改善高并发用户访问情况下的网站响应速度。

网站性能优化的最终目的是改善用户的体验，但性能优化本身也是需要综合考虑的，比如说：性能提高一倍，服务器数量也要增加一倍，这样的优化是否可以考虑。



1.Django开发过程中数据库的优化

    1.设计表时，尽量少使用外键，因为外键约束会影响插入和删除性能。可参照以下文章——“58到家数据库30条军规解读”：https://blog.csdn.net/qq_38923792/article/details/93135629
    
     禁止使用外键，如果有外键完整性约束，需要应用程序控制
解读：外键会导致表与表之间耦合，update与delete操作都会涉及相关联的表，十分影响sql 的性能，甚至会造成死锁。高并发情况下容易造成数据库性能，大数据高并发业务场景数据库使用以性能优先。

    

关于如何体现表与表之间的关联性和如何维护数据完整性和一致性：

1.关联性：那就是设计数据库的时候，要让所有人知道表与表之间的通过那个字段关联起来，所以字段名称命名上会做一些文章。

2. 如何维护数据完整性和一致性：通过外部程序的力量，启用事务的方式，比如：
START TRANSACTION;
UPDATE A SET co1=** …;
UPDATE B SET A_co1=**…;
COMMIT;
注释：假设场景 A表的col1变成某值之后，B表中的A_col1字段也必须修改为对应的值`…

   
   
    2.使用缓存，减少对数据库的访问。
    3.在ORM框架下设置表时，能用varchar确定字段长度时，就不用text；
    4.可以给搜索频率高的字段属性，在定义时创建索引。
    如果对多个字段创建索引以后，注意查询的顺序遵循最左前缀原则。即创建的顺序是name,age,country,查询时可以是只查name，或者只查name,age，或者按顺序查找name,age,country，否则其他顺序都将进行全表扫描。
    5.Django ORM框架下的Queryset本来就是有缓存的。
    6.如果一个页面需要多次连接数据库，最好一次性取出所有数据，减少对数据库的查询次数。
    7.若页面只需要数据库里一两个字段时，可以用Queryset.values()
    8.在模板标签里使用with标签可以缓存Queryset的查询结果。
 9.对于多张表联合查询的，查询频率较高的字段可以在其关联的另一张表设置冗余字段，例如sales销量、评论量、默认图片地址default_img_url等，以空间换时间。根据具体项目权衡。

2.django开发 如何提升性能（高并发）？

从三个方面进行优化：web前端性能优化，web服务器端的性能优化，运维部署的优化。
2.1web前端优化

    1.减少http请求，减少数据库的访问量，比如使用雪碧图。
    
    【雪碧图是根据CSS sprite音译过来的，就是将很多很多的小图标放在一张图片上，就称之为雪碧图。
    使用雪碧图的目的：
    有时为了美观，我们会使用一张图片来代替一些小图标，但是一个网页可能有很多很多的小图标，浏览器在显示页面的时候，就需要像服务器发送很多次访问请求，这样一来，一是造成资源浪费，二是会导致访问速度变慢。这时候，把很多小图片（需要使用的小图标）放在一张图片上，按照一定的距离隔开，就解决了上述的两个问题。】
    
    
    
    2 .使用浏览器缓存，将一些常用的css/js/logo图标这些静态资源缓存到本地浏览器，通过设置http头中的cache-control 和expires属性，可设定浏览器缓存，缓存时间可以自定义。
    对html、css、js文件进行压缩，减少网络通信量。

2.2 web后端优化

    1.使用合理的缓存技术，对一些常用到的动态数据，比如首页做一个缓存，或者某些常用的数据做缓存，设置一定的过期时间，这样减少了数据库的压力，提升网站性能。
    2.使用celery消息队列，将耗时的操作丢到队列里，让worker去监听队列的任务，实现异步操作，比如发短信、发邮件。
    3.代码上的优化，避免写冗余代码。
    4.数据库查询语句的优化，对于建了索引的字段查询时遵循最左原则，避免出现全表扫描。

2.3.运维部署优化

    1.搭建nginx服务器，搭建服务器集群，充分利用nginx服务器的负载均衡和反向代理功能。
    2.数据库设置读写分离，可对读的那台数据库使用myisam引擎。写的那台使用innodb引擎。

3.代码优化从哪些方面考虑？有什么想法？
3.1 优化算法时间

算法的时间复杂度对程序的执行效率影响最大，在python中选择合适的数据结构来优化时间复杂度。例如list和set查找一个元素的时间复杂度分别是O（n）和O（1）.不同场景不同的优化方式。总得来说，一般有分治，分支界限，贪心，动态规划等思想。
3.2 循环优化

减少python在循环内部的执行的工作量.例如列表推导式和字典推导式。
3.3 并行编程

多进程：对于CPU 密集型的程序，可以使用multiprocessing 的Process，Pool 等封装好的类，通过多进程的方式实现并行计算。
多线程：对于IO 密集型的程序，multiprocessing.dummy 模块使用multiprocessing 的接口封装threading，使得多线程编程也变得非常轻松(比如可以使用Pool 的map 接口，简洁高效)。
布式：multiprocessing 中的Managers 类提供了可以在不同进程之共享数据的方式，可以在此基础上开发出分布式的程序。
3.4 使用性能分析工具

除了上面在ipython 使用到的timeit 模块，还有cProfile。cProfile 的使用方式也非常简单：python-mcProfilefilename.py，filename.py 是要运行程序的文件名，可以在标准输出中看到每一个函数被调用的次数和运行的时间，从而找到程序的性能瓶颈，然后可以有针对性地优化。
3.5 合理使用copy与deepcopy

对于dict和list等数据结构的对象，直接赋值使用的是引用的方式。而有些情况下需要复制整个对象，这时可以使用copy包里的copy和deepcopy，这两个函数的不同之处在于后者是递归复制的。效率也不一样。（以下程序在ipython中运行）

    import copy
    a = range(100000)
    %timeit -n 10 copy.copy(a)     # 运行10次 copy.copy(a)
    %timeit -n 10 copy.deepcopy(a)
    10 loops, best of 3: 1.55 ms per loop
    10 loops, best of 3: 151 ms per loop
    
    

timeit后面的-n表示运行的次数，后两行对应的是两个timeit的输出，下同。由此可见后者慢一个数量级。
3.6使用dict或set查找元素

python dict和set都是使用hash表来实现(类似c++11标准库中unordered_map)，查找元素的时间复杂度是O(1)
3.7 合理使用生成器（generator）和yield
3.8 不借助中间变量交换两个变量的值（a,b=b,a）
3.9 终级大杀器：PyPy

PyPy是用RPython(CPython的子集)实现的Python，根据官网的基准测试数据，它比CPython实现的Python要快6倍以上。快的原因是使用了Just-in-Time(JIT)编译器，即动态编译器，与静态编译器(如gcc,javac等)不同，它是利用程序运行的过程的数据进行优化。由于历史原因，目前pypy中还保留着GIL，不过正在进行的STM项目试图将PyPy变成没有GIL的Python。

如果python程序中含有C扩展(非cffi的方式)，JIT的优化效果会大打折扣，甚至比CPython慢（比Numpy）。所以在PyPy中最好用纯Python或使用cffi扩展。

随着STM，Numpy等项目的完善，相信PyPy将会替代CPython。
3.10 使用性能分析工具

除了上面在ipython使用到的timeit模块，还有cProfile。cProfile的使用方式也非常简单： python -m cProfile filename.py，filename.py 是要运行程序的文件名，可以在标准输出中看到每一个函数被调用的次数和运行的时间，从而找到程序的性能瓶颈，然后可以有针对性地优化。


