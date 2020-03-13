---
title: aiohttp学习
date: 2019-10-04 21:23:48
tags:
---

`asyncio`可以实现单线程并发IO操作。如果仅用在客户端，发挥的威力不大。如果把`asyncio`用在服务器端，例如Web服务器，由于HTTP连接就是IO操作，因此可以用单线程+`coroutine`实现多用户的高并发支持。

<!--more-->

`asyncio`实现了TCP、UDP、SSL等协议，`aiohttp`则是基于`asyncio`实现的HTTP框架。

我们先安装`aiohttp`：

```
pip install aiohttp
```

然后编写一个HTTP服务器，分别处理以下URL：

- `/` - 首页返回`b'<h1>Index</h1>'`；
- `/hello/{name}` - 根据URL参数返回文本`hello, %s!`。

代码如下：

```
import asyncio

from aiohttp import web

async def index(request):
    await asyncio.sleep(0.5)
    return web.Response(body=b'<h1>Index</h1>')

async def hello(request):
    await asyncio.sleep(0.5)
    text = '<h1>hello, %s!</h1>' % request.match_info['name']
    return web.Response(body=text.encode('utf-8'))

async def init(loop):
    app = web.Application(loop=loop)
    app.router.add_route('GET', '/', index)
    app.router.add_route('GET', '/hello/{name}', hello)
    srv = await loop.create_server(app.make_handler(), '127.0.0.1', 8000)
    print('Server started at http://127.0.0.1:8000...')
    return srv

loop = asyncio.get_event_loop()
loop.run_until_complete(init(loop))
loop.run_forever()
```

注意`aiohttp`的初始化函数`init()`也是一个`coroutine`，`loop.create_server()`则利用`asyncio`创建TCP服务。





# 1.aiohttp的简单使用(配合asyncio模块)

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
import asyncio,aiohttp

async def fetch_async(url):
    print(url)
    async with aiohttp.request("GET",url) as r:
        reponse = await r.text(encoding="utf-8")　　#或者直接await r.read()不编码，直接读取，适合于图像等无法编码文件
        print(reponse)

tasks = [fetch_async('http://www.baidu.com/'), fetch_async('http://www.chouti.com/')]

event_loop = asyncio.get_event_loop()
results = event_loop.run_until_complete(asyncio.gather(*tasks))
event_loop.close()
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

# 2.发起一个session请求

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
import asyncio,aiohttp

async def fetch_async(url):
    print(url)
    async with aiohttp.ClientSession() as session:　　#协程嵌套，只需要处理最外层协程即可fetch_async
        async with session.get(url) as resp:
            print(resp.status)
            print(await resp.text())　　#因为这里使用到了await关键字，实现异步，所有他上面的函数体需要声明为异步async

tasks = [fetch_async('http://www.baidu.com/'), fetch_async('http://www.cnblogs.com/ssyfj/')]

event_loop = asyncio.get_event_loop()
results = event_loop.run_until_complete(asyncio.gather(*tasks))
event_loop.close()
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

### 除了上面的get方法外，会话还支持post，put,delete....等

```
session.put('http://httpbin.org/put', data=b'data')
session.delete('http://httpbin.org/delete')
session.head('http://httpbin.org/get')
session.options('http://httpbin.org/get')
session.patch('http://httpbin.org/patch', data=b'data')
```

**不要为每次的连接都创建一次session,一般情况下只需要创建一个session，然后使用这个session执行所有的请求。**

**每个session对象，内部包含了一个连接池，并且将会保持连接和连接复用（默认开启）可以加快整体的性能。**

# 3.在url中传递参数（其实与requests模块使用大致相同）

### 只需要将参数字典，传入params参数中即可

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
import asyncio,aiohttpasync def func1(url,params):    async with aiohttp.ClientSession() as session:        async with session.get(url,params=params) as r:            print(r.url)            print(await r.read())tasks = [func1('https://www.ckook.com/forum.php',{"gid":6}),]event_loop = asyncio.get_event_loop()results = event_loop.run_until_complete(asyncio.gather(*tasks))event_loop.close()
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

# 4.获取响应内容（由于获取响应内容是一个阻塞耗时过程，所以我们使用await实现协程切换）

### （1）使用text()方法

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
async def func1(url,params):
    async with aiohttp.ClientSession() as session:
        async with session.get(url,params=params) as r:
            print(r.url)
            print(r.charset)　　#查看默认编码为utf-8
            print(await r.text())　　#不编码，则是使用默认编码　　使用encoding指定编码
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

### （2）使用read()方法，不进行编码，为字节形式

```
async def func1(url,params):
    async with aiohttp.ClientSession() as session:
        async with session.get(url,params=params) as r:
            print(r.url)
            print(await r.read())
```

### （3）注意：text(),read()方法是把整个响应体读入内存，如果你是获取大量的数据，请考虑使用”字节流“（StreamResponse）

# 5.特殊响应内容json（和上面一样）

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
async def func1(url,params):
    async with aiohttp.ClientSession() as session:
        async with session.get(url,params=params) as r:
            print(r.url)
            print(r.charset)
            print(await r.json())　　#可以设置编码，设置处理函数
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

# 6.字节流形式获取数据（不像text,read一次获取所有数据）

### 注意：我们获取的session.get()是Response对象，他继承于StreamResponse

```
async def func1(url,params):
    async with aiohttp.ClientSession() as session:
        async with session.get(url,params=params) as r:
            print(await r.content.read(10))    #读取前10字节
```

### 下面字节流形式读取数据，保存文件

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
async def func1(url,params,filename):
    async with aiohttp.ClientSession() as session:
        async with session.get(url,params=params) as r:
            with open(filename,"wb") as fp:
                while True:
                    chunk = await r.content.read(10)
                    if not chunk:
                        break
                    fp.write(chunk)

tasks = [func1('https://www.ckook.com/forum.php',{"gid":6},"1.html"),]
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

### 注意：

```
async with session.get(url,params=params) as r:　　#异步上下文管理器
with open(filename,"wb") as fp:　　#普通上下文管理器
```

### 两者的区别：

在于异步上下文管理器中定义了

**__aenter__和__aexit__方法**

**![img](https://images2018.cnblogs.com/blog/1309518/201806/1309518-20180624223315415-323203222.png)**

异步上下文管理器指的是在`enter`和`exit`方法处能够暂停执行的上下文管理器

 为了实现这样的功能，需要加入两个新的方法：`_**_aenter__**` **和__aexit__。这两个方法都要返回一个 awaitable类型的值。**

**推文：异步上下文管理器async with和异步迭代器async for**

# 7.自定义请求头（和requests一样）

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
async def func1(url,params,filename):
    async with aiohttp.ClientSession() as session:
        headers = {'Content-Type':'text/html; charset=utf-8'}
        async with session.get(url,params=params,headers=headers) as r:
            with open(filename,"wb") as fp:
                while True:
                    chunk = await r.content.read(10)
                    if not chunk:
                        break
                    fp.write(chunk)
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

# 8.自定义cookie

### 注意：对于自定义cookie，我们需要设置在ClientSession(cookies=自定义cookie字典),而不是session.get()中

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
class ClientSession:

    def __init__(self, *, connector=None, loop=None, cookies=None,
                 headers=None, skip_auto_headers=None,
                 auth=None, json_serialize=json.dumps,
                 request_class=ClientRequest, response_class=ClientResponse,
                 ws_response_class=ClientWebSocketResponse,
                 version=http.HttpVersion11,
                 cookie_jar=None, connector_owner=True, raise_for_status=False,
                 read_timeout=sentinel, conn_timeout=None,
                 timeout=sentinel,
                 auto_decompress=True, trust_env=False,
                 trace_configs=None):
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

使用：

```
cookies = {'cookies_are': 'working'}
async with ClientSession(cookies=cookies) as session:
```

# 9.获取当前访问网站的cookie

```
async with session.get(url) as resp:
    print(resp.cookies)
```

# 10.获取网站的响应状态码

```
async with session.get(url) as resp:
    print(resp.status)
```

# 11.查看响应头

```
resp.headers 来查看响应头，得到的值类型是一个dict：
resp.raw_headers　　查看原生的响应头，字节类型
```

# 12.查看重定向的响应头（我们此时已经到了新的网址，向之前的网址查看）

```
resp.history　　#查看被重定向之前的响应头
```

# 13.超时处理

默认的IO操作都有5分钟的响应时间 我们可以通过 timeout 进行重写：

```
async with session.get('https://github.com', timeout=60) as r:
    ...
```

如果 timeout=None 或者 timeout=0 将不进行超时检查，也就是不限时长。

# 14.ClientSession 用于在多个连接之间（同一网站）共享cookie，请求头等

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
async def func1():
    cookies = {'my_cookie': "my_value"}
    async with aiohttp.ClientSession(cookies=cookies) as session:
        async with session.get("https://segmentfault.com/q/1010000007987098") as r:
            print(session.cookie_jar.filter_cookies("https://segmentfault.com"))
        async with session.get("https://segmentfault.com/hottest") as rp:
            print(session.cookie_jar.filter_cookies("https://segmentfault.com"))
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
Set-Cookie: PHPSESSID=web2~d8grl63pegika2202s8184ct2q
Set-Cookie: my_cookie=my_value
Set-Cookie: PHPSESSID=web2~d8grl63pegika2202s8184ct2q
Set-Cookie: my_cookie=my_value
```

我们最好使用session.cookie_jar.filter_cookies()获取网站cookie，不同于requests模块，虽然我们可以使用rp.cookies有可能获取到cookie，但似乎并未获取到所有的cookies。

![img](https://images2018.cnblogs.com/blog/1309518/201806/1309518-20180625095126280-1151840661.png)

------

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
async def func1():
    cookies = {'my_cookie': "my_value"}
    async with aiohttp.ClientSession(cookies=cookies) as session:
        async with session.get("https://segmentfault.com/q/1010000007987098") as rp:
            print(session.cookie_jar.filter_cookies("https://segmentfault.com"))
            print(rp.cookies)　　#Set-Cookie: PHPSESSID=web2~jh3ouqoabvr4e72f87vtherkp6; Domain=segmentfault.com; Path=/　　#首次访问会获取网站设置的cookie
        async with session.get("https://segmentfault.com/hottest") as rp:
            print(session.cookie_jar.filter_cookies("https://segmentfault.com"))
            print(rp.cookies)　　#为空，服务端未设置cookie
        async with session.get("https://segmentfault.com/newest") as rp:
            print(session.cookie_jar.filter_cookies("https://segmentfault.com"))
            print(rp.cookies)　　#为空，服务端未设置cookie
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

![img](https://images2018.cnblogs.com/blog/1309518/201806/1309518-20180625095530905-938944868.png)

## 总结：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
当我们使用rp.cookie时，只会获取到当前url下设置的cookie,不会维护整站的cookie

而session.cookie_jar.filter_cookies("https://segmentfault.com")会一直保留这个网站的所有设置cookies，含有我们在会话时设置的cookie，并且会根据响应修改更新cookie。这个才是我们需要的

而我们设置cookie，也是需要在aiohttp.ClientSession(cookies=cookies)中设置
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

ClientSession 还支持 请求头，keep-alive连接和连接池(connection pooling)

# 15.cookie的安全性

默认ClientSession使用的是严格模式的 aiohttp.CookieJar. RFC  2109，明确的禁止接受url和ip地址产生的cookie，只能接受 DNS  解析IP产生的cookie。可以通过设置aiohttp.CookieJar 的 unsafe=True 来配置：

```
jar = aiohttp.CookieJar(unsafe=True)
session = aiohttp.ClientSession(cookie_jar=jar)
```

# 16.控制同时连接的数量（连接池）

TCPConnector维持链接池，限制并行连接的总量，当池满了，有请求退出再加入新请求

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
async def func1():
    cookies = {'my_cookie': "my_value"}
    conn = aiohttp.TCPConnector(limit=2)　　#默认100，0表示无限
    async with aiohttp.ClientSession(cookies=cookies,connector=conn) as session:
        for i in range(7,35):
            url = "https://www.ckook.com/list-%s-1.html"%i
            async with session.get(url) as rp:
                print('---------------------------------')
                print(rp.status)
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

限制同时打开限制同时打开连接到同一端点的数量（(host, port, is_ssl) 三的倍数），可以通过设置 limit_per_host 参数：

limit_per_host： 同一端点的最大连接数量。同一端点即(host, port, is_ssl)完全相同

```
conn = aiohttp.TCPConnector(limit_per_host=30)#默认是0
```

在协程下测试效果不明显

# 17.自定义域名解析地址

我们可以指定域名服务器的 IP 对我们提供的get或post的url进行解析：

```
from aiohttp.resolver import AsyncResolver
 
resolver = AsyncResolver(nameservers=["8.8.8.8", "8.8.4.4"])
conn = aiohttp.TCPConnector(resolver=resolver)
```

# 18.设置代理

aiohttp支持使用代理来访问网页：

```
async with aiohttp.ClientSession() as session:
    async with session.get("http://python.org",
                           proxy="http://some.proxy.com") as resp:
        print(resp.status)
```

当然也支持需要授权的页面：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
async with aiohttp.ClientSession() as session:
    proxy_auth = aiohttp.BasicAuth('user', 'pass')　　#用户，密码
    async with session.get("http://python.org",
                           proxy="http://some.proxy.com",
                           proxy_auth=proxy_auth) as resp:
        print(resp.status)
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

或者通过这种方式来验证授权：

```
session.get("http://python.org",
            proxy="http://user:pass@some.proxy.com")
```

# 19.post传递数据的方法

## （1）模拟表单

```
payload = {'key1': 'value1', 'key2': 'value2'}
async with session.post('http://httpbin.org/post',
                        data=payload) as resp:
    print(await resp.text())
```

注意：data=dict的方式post的数据将被转码，和form提交数据是一样的作用，如果你不想被转码，可以直接以字符串的形式 data=str 提交，这样就不会被转码。

## （2）post json

```
payload = {'some': 'data'}
 
async with session.post(url, data=json.dumps(payload)) as resp:
```

其实json.dumps(payload)返回的也是一个字符串，只不过这个字符串可以被识别为json格式

## （3）post 小文件

```
url = 'http://httpbin.org/post'
files = {'file': open('report.xls', 'rb')}
 
await session.post(url, data=files)
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
url = 'http://httpbin.org/post'
data = FormData()
data.add_field('file',
               open('report.xls', 'rb'),
               filename='report.xls',
               content_type='application/vnd.ms-excel')
 
await session.post(url, data=data)
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

如果将文件对象设置为数据参数，aiohttp将自动以字节流的形式发送给服务器。

## （4）post 大文件

aiohttp支持多种类型的文件以流媒体的形式上传，所以我们可以在文件未读入内存的情况下发送大文件。

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
@aiohttp.streamer
def file_sender(writer, file_name=None):
    with open(file_name, 'rb') as f:
        chunk = f.read(2**16)
        while chunk:
            yield from writer.write(chunk)
            chunk = f.read(2**16)
 
# Then you can use `file_sender` as a data provider:
 
async with session.post('http://httpbin.org/post',
                        data=file_sender(file_name='huge_file')) as resp:
    print(await resp.text())
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

## （5）从一个url获取文件后，直接post给另一个url

```
r = await session.get('http://python.org')
await session.post('http://httpbin.org/post',data=r.content)
```

## （6）post预压缩数据

在通过aiohttp发送前就已经压缩的数据, 调用压缩函数的函数名（通常是deflate 或 zlib）作为content-encoding的值：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
async def my_coroutine(session, headers, my_data):
    data = zlib.compress(my_data)
    headers = {'Content-Encoding': 'deflate'}
    async with session.post('http://httpbin.org/post',
                            data=data,
                            headers=headers)
        pass
```