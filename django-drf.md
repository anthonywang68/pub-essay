---
title: django---drf
date: 2019-09-29 11:00:52
tags:
---

DRF梳理(不断更新....)

REST是一种软件架构设计风格，不是标准，也不是技术实现，只是提供了一组设计原则和约束条件，是目前最流行的API 设计规范，用于web数据接口的设计。2000年，由Roy Fielding在他的博士论文中提出，Roy Fielding是HTTP规范的主要编写者之一。

　　RESTful参考链接：

　　　　[RESTful API 最佳实践 - 阮一峰](http://www.ruanyifeng.com/blog/2018/10/restful-api-best-practices.html)

　　　　[我所认为的RESTful API最佳实践](http://www.scienjus.com/my-restful-api-best-practices/)

<!--more-->

其实，DRF（Django REST Framework）是一套基于django开发的，帮助我们更好的设计符合REST规范的web应用的一个Django App，所以，本质上，它是要给Django App。

[回到顶部](https://www.cnblogs.com/li-li/p/10072936.html#main)

#### **二、****知识准备**

　　学习新知识之前，先回顾以下几个知识点：

1、CBV（class based view）

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
　　from django.views import View
　　class LoginView(View):
    　　def get(self, request):
        　　pass
　　　　def post(self, request):
　　　　　　pass
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

2、 类方法classmethod & classonlymethod

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
class Person(object):
    def __init__(self, name, age):
        self.name = name
        self.age = age

　　 # 注意：Person类加载时，会执行装饰器函数classmethod(sleepping),并将结果赋给slepping
    @classmethod          # 相当于 sleepping = classmethod(sleepping)
    def sleepping(cls):
        print("Jihong is sleepping")

    @classonlymethod
    def guangjie(cls):
         print("Jihong is shopping")

Person.sleepping()   # 类直接调用类方法
Person.guangjie()    # 类直接调用方法
jihong = Person("jihong", 20)
jihong.sleepping()   # 对象可以调用类方法
jihong.guangjie()    # 报错，对象不能调用由@classonlymethod装饰的方法
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　总结：

　　　　@classmethod装饰（python加的装饰器）的方法可以由对象和类调用；

　　　　@classonlymethod装饰（django加的装饰器）只能由类直接调用；

3、反射

　　getattr, hasattr, setattr

4、self定位

　　明确self指向谁 - 始终指向调用者

5、http请求协议

　　协议就是沟通双方约定俗称的规范，即解析数据的规则。

6、form表单的enctype属性中有三种请求协议

　　如果通过form表单提交用户数据，可以使用form表单的enctype属性来定义数据编码协议，该属性有三个值，代表三种数据编码协议：

　　- application/x-www-form-urlencoded：使用&符号连接多个键值对，键值对用等号拼接，默认；

　　- multipart/form-data：上传文件、图片时使用该方式；

　　- text/plain：空格转换为“+”号；

7、JavaScript中 object，例如： { name: "pizza"}  <==>  json 的相互转换方式

　　JSON.stringify(data)  =相当于=>  python json.dumps()

　　JSON.parse(data)  =相当于=>  python json.loads()

[回到顶部](https://www.cnblogs.com/li-li/p/10072936.html#main)

#### **三、****Django REST Framework（DRF）**

　　为什么使用DRF?

　　从概念可以看出，有了这样的一个App，能够帮助我们更好的设计符合RESTful规范的web应用，实际上，没有它，我们也能自己设计符合规范的web应用，如下代码中，我们就手动实现了符合RESTful规范的web应用：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
class CourseView(View):
    def get(self, request):
        course_list = list()

        for course in Course.objects.all():
            course = {
                "course_name": course.course_name,
                "description": course.description
            }

            course_list.append(course)

        return HttpResponse(json.dumps(course_list, ensure_ascii=False))
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　如上代码所示，我们获取所有的课程数据，并根据REST规范，将所有资源通过列表返回给用户，可见，就算没有DRF，我们也能设计出符合RESTful规范的接口，甚至是整个App应用，但是，如果所有的接口都自定义，难免会出现重复代码，为了提高工作效率，我们建议使用优秀的工具，DRF就是这样一个优秀的工具，另外，它不仅能够帮助我们快速的设计符合REST规范的接口，还提供诸如认证、权限等等其他强大功能。

　　什么时候使用DRF?

　　前边提到，REST是目前最流行的API设计规范，如果使用Django开发你的web应用，那么请尽量使用DRF，如果使用的是Flask，可以使用Flask-RESRful。

　　[DRF官方文档](https://www.django-rest-framework.org/)中有详细介绍，使用如下命令安装，首先安装Django，然后安装DRF：

```
`>>> pip install django   ``# 安装django，已经安装的可以直接下载DRF``>>> pip install djangorestframework   ``# 下载`
```

　　安装完成之后，我们就可以开始使用DRF框架来实现我们的web应用了，这部分内容包括如下知识点：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
　　- APIView
　　- 解析器组件
　　- 序列化组件
　　- 视图组件
　　- 认证组件
　　- 权限组件
　　- 频率控制组件
　　- 分页组件
　　- 相应器组件
　　- url控制器
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　介绍DRF，必须介绍APIView，它是重中之重，是下面所有组件的基础，因为所有的请求都是通过它来分发的，至于它究竟是如何分发请求的？想要弄明白这个问题，就必须解读它的源码，而想要解读DRF APIView的源码，就必须先解读django中views.View类的源码，为什么使用视图类调用as_view()之后，请求就可以被不同的函数处理？

1、回顾CBV，解读View源码

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
# views.py中代码如下：
from django.views import View
class LoginView(View):
　　def get(self, request):
　　　　pass
　　def post(self, request):
　　　　pass

# urls.py中代码如下：
from django.urls import path, include, re_path
from classbasedView import views
urlpatterns = [
　　re_path("login/$", views.LoginView.as_view())
]
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　1）启动django项目：python manage.py runserver 127.0.0.1:8000后

　　2）开始加载settings配置文件

　　　　- 读取models.py

　　　　- 加载views.py

　　　　- 加载urls.py，执行as_view( )： views.LoginView.as_view()

　　　　LoginView中没有as_view，因此去执行父类View中as_view方法，父类View的相关源码如下：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
class View:
　　http_method_names = ['get', 'post', 'put', ...]
    def __init__(self, **kwargs):
        for key, value in kwargs.items():
            setattr(self, key, value)

    @classonlymethod
    def as_view(cls, **initkwargs):
        for key in initkwargs:
            ...
        def view(request, *args, **kwargs):
　　　　　　  # 实例化一个对象，对象名称为self，self是cls的对象，谁调用了as_view
　　　　　　  # cls就是谁（当前调用as_view的是LoginView）
　　　　　　  # 所以，此时的self就是LoginView的实例化对象
            self = cls(**initkwargs)  
            if hasattr(self, 'get') and not hasattr(self, 'head'):
                self.head = self.get
            self.request = request
            self.args = args
            self.kwargs = kwargs
            return self.dispatch(request, *args, **kwargs)
        view.view_class = cls
        view.view_initkwargs = initkwargs

        update_wrapper(view, cls, updated=())
        update_wrapper(view, cls.dispatch, assigned=())
        return view
　　def dispatch(self, request, *args, **kwargs):
       if request.method.lower() in self.http_method_names:
       　　 # 通过getattr找到的属性，已和对象绑定，访问时不需要再指明对象了
        　　# 即不需要再：self.handler，直接handler()执行
           handler = getattr(self, request.method.lower(), self.http_method_not_allowed)
       else:
           handler = self.http_method_not_allowed
       return handler(request, *args, **kwargs)
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　　　上面源码中可以看出，as_view是一个类方法，并且方法中定义了view函数，且as_view将view函数返回，此时url与某一个函数的对应关系建立，并开始等待用户请求。

　　3）当用户发来请求（如get请求），开始执行url对应的view函数，并传入request对象，view函数的执行结果是返回self.dispatch(request, *args, **kwargs)的执行结果，这里的self是指LoginView的实例化对象，LoginView中没有dispatch方法，所以去执行父类View中的dispatch方法，View类中的dispatch函数中通过反射找到self（此时self指LoginView的实例对象）所属类（即LoginView）中的get方法，dispatch函数中的handler(request, *args, **kwargs)表示执行LoginView类中的get方法，其执行结果就是dispatch的执行结果，也就是请求url对应view函数的执行结果，最后将结果返回给用户。

2、APIView

　　使用：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
# views.py中代码：
from rest_framework.views import APIView       # 引入APIView
class LoginView(APIView):   # LoginView继承APIView
　　def get(self, request):
　　　　pass
　　def post(self, request):
　　　　pass

# urls.py中用法同CBV一样，如下示例：
from django.urls import path, include, re_path
from classbasedView import views
urlpatterns = [
　　re_path("login/$", views.LoginView.as_view())
]
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　源码解读：

　　　　1）启动django项目：python manage.py runserver 127.0.0.1:8000后

　　　　2）开始加载settings配置文件

　　　　　　- 读取models.py

　　　　　　- 加载views.py

　　　　　　- 加载urls.py，执行as_view( )： views.LoginView.as_view()

　　　　　　LoginView中没有as_view，因此去执行父类APIView中as_view方法，父类APIView的相关源码如下：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
class APIView(View):
    ...
　　 # api_settings是APISettings类的实例化对象
    parser_classes = api_settings.DEFAULT_PARSER_CLASSES
    ...
    settings = api_settings
    schema = DefaultSchema()

    @classmethod
    def as_view(cls, **initkwargs):   # cls指LoginView
        if isinstance(getattr(cls, 'queryset', None), models.query.QuerySet):
            ...
     　　# 下面一句表示去执行APIView父类（即View类）中的as_view方法
        view = super(APIView, cls).as_view(**initkwargs)
        view.cls = cls
        view.initkwargs = initkwargs
        return csrf_exempt(view)

　　 def dispatch(self, request, *args, **kwargs):
    　　...
    　　request = self.initialize_request(request, *args, **kwargs)
    　　...
    　　try:
        　　self.initial(request, *args, **kwargs)
        　　if request.method.lower() in self.http_method_names:
            　　handler = getattr(self, request.method.lower(), self.http_method_not_allowed)
        　　else:
            　　handler = self.http_method_not_allowed

        　　response = handler(request, *args, **kwargs)

    　　except Exception as exc:
        　　response = self.handle_exception(exc)

    　　self.response = self.finalize_response(request, response, *args, **kwargs)
    　　return self.response
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　　　　　参考View源码解读，我们知道View中的as_view方法返回view函数，此时url与view的对应关系已经建立，等待用户请求。

　　　　3）当用户发来请求（如get请求），开始执行url对应的view函数，并传入request对象，View类中view函数的执行结果是返回self.dispatch(request, *args, **kwargs)的执行结果，这里的self是指LoginView的实例化对象，LoginView中没有dispatch方法，所以去执行父类APIView中的dispatch方法，同样，APIView类中的dispatch函数中也是通过反射找到self（此时self指LoginView的实例对象）所属类（即LoginView）中的get方法，dispatch函数中的handler(request, *args, **kwargs)表示执行LoginView类中的get方法，其执行结果就是dispatch的执行结果，也就是请求url对应view函数的执行结果，最后将结果返回给用户。

[回到顶部](https://www.cnblogs.com/li-li/p/10072936.html#main)

#### **四、补充知识点**

1、若类中有装饰器函数，那么当类加载的时候，装饰器函数就会执行，如下代码：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
class Person(object):
@classmethod          # 相当于 sleepping = classmethod(sleepping)
　　def sleepping(cls):
　　　　print("Jihong is sleepping")

　　print(sleepping)  # 加载类时执行，结果<classmethod object at 0x000001F2C29C8198>
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　注意：类中直接print语句会执行打印输出结果，而函数只有调用时才会执行，如下：

```
def func():
　　print('hello world')  # 函数func加载不会执行打印，只有调用即func()才会执行打印
```

2、__dict__方法

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
class Person(object):
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def sing(self):
        print('I am singing')

p1 = Person('alex', 18)
print(p1.__dict__)     # {'name': 'alex', 'age': 18}
print(Person.__dict__)  
# {'__module__': '__main__', '__init__': <function Person.__init__ at 0x0000021E1A46A8C8>, 
'sing': <function Person.sing at 0x0000021E1A46A950>, 
'__dict__': <attribute '__dict__' of 'Person' objects>, 
'__weakref__': <attribute '__weakref__' of 'Person' objects>, 
'__doc__': None}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　总结：

　　　　对象.__dict__ 返回对象的所有成员字典；

　　　　类.__dict__ 返回类的所有成员字典；

　　　　我们可以通过（对象.name）取出成员，字典没有这种取值方式，使用对象.name的本质是执行类中的__getitem__方法。

3、现在有如下两个需求：

 

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
# 需求一：计算add函数的执行时间（不重写add函数的前提下）
def add(x, y):
　　return x+y
# 解决方式：装饰器
def outer(func):
　　def inner(*args, **kwargs):
　　　　import time
　　　　start_time = time.time()
　　　　ret = func(*args, **kwargs)
　　　　end_time = time.time()
　　　　print(end_time - start_time)
　　return inner

@outer
def add(x, y):
　　return x+y
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

 

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
# 需求二：扩展类中函数的功能（在不重写Father类的前提下）
class Father(object):
　　def show(self):
　　　　print('father show is excuted')
father = Father()
father.show()
# 解决方式：重新写一个类，继承Father类，重写show(),super()调用
class Son(Father):
　　def show(self):
　　　　print('son show is excuted')
　　　　super().show()
son = Son()
son.show()
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　总结：

　　　　面向过程的方式对程序进行功能扩展

　　　　　　- 装饰器

　　　　面向对象的方式对程序功能进行扩展

　　　　　　- 类的继承

　　　　　　- 方法重写

　　　　　　- super()





##  						Django -- DRF 中的 视图 详解 					

技术标签： [DRF](http://www.pianshen.com/tag/DRF/)  [视图](http://www.pianshen.com/tag/视图/)  [Django](http://www.pianshen.com/tag/Django/)  [基类](http://www.pianshen.com/tag/基类/)  [可扩展类](http://www.pianshen.com/tag/可扩展类/)



## 两个基类的介绍

Ⅰ. **APIView**
 介绍：
 `APIView`是REST framework提供的所有视图的基类，继承自Django的View父类。

**APIView与View的不同之处在于**：

- 传入到视图方法中的是REST framework的Request对象，而不是Django的HttpRequeset对象；
- 视图方法可以返回REST framework的Response对象，视图会为响应数据设置（render）符合前端要求的格式；
- 任何APIException异常都会被捕获到，并且处理成合适的响应信息；
- 在进行dispatch()分发前，会对请求进行身份认证、权限检查、流量控制。

------

  案例一：完成所有书籍的展示查询 

继承于APIView，代码实现如下：

```
#  views.py 部分代码
from rest_framework.views import APIView
from rest_framework.response import Response


class BookInfoAPIView(APIView):
    books = BookInfo.objects.all()
    serializer = BookInfoModelSerializer

    def get(self, request):
        s = self.serializer(self.books, many=True)
        return Response(s.data)

12345678910111213
#  urls.py 中的 url
url(r'books/', views.BookInfoAPIView.as_view())
12
```

------

Ⅱ. **GenericAPIView**

介绍：
 继承自**APIView**，增加了对于列表视图和详情视图可能用到的通用支持方法。通常使用时，可搭配一个或多个Mixin扩展类。

**支持定义的属性**：

1. 列表视图与详情视图通用：

- queryset 列表视图的查询集
- serializer_class 视图使用的序列化器

1. 列表视图使用：

- pagination_class 分页控制类
- filter_backends 过滤控制后端

1. 详情页视图使用：

- lookup_field：查询单一数据库对象时使用的条件字段，默认为’pk’
- lookup_url_kwarg   ：查询单一数据时URL中的参数关键字名称，默认与look_field相同。

**提供的方法：**

**列表视图与详情视图通用**:

- get_queryset : 获取视图对应的查询集，是列表视图和详细视图获取数据的基础；默认返回的是`queryset` 的属性，可重写
- get_serializer_class : 获取序列化器类，默认返回的是`serializer_class`，可重写；
- get_serializer(self, args, *kwargs) :
   获取序列化器对象，这一步相对于APIView来说，就免去了创建序列化对象；

**详情视图使用**:

*get_object(self)* :  返回详情视图所需的模型类数据对象，默认使用lookup_field参数来过滤queryset。 在试图中可以调用该方法获取详情信息的模型类对象。

若详情访问的模型类对象不存在，会返回404。

该方法会默认使用APIView提供的check_object_permissions方法检查当前对象是否有权限被访问。

------

案例二：需求：查询某个书籍的具体信息

继承GenericAPIView实现的代码：

```
#  views.py 部分代码
class BookInfoAPIView(GenericAPIView):
    # 定义视图的查询集
    queryset = BookInfo.objects.all()
    # 指定视图对应的序列化器
    serializer_class = BookInfoModelSerializer

    def get(self, request, pk):
        # 需求：　查询某个书籍的详细信息
        book = self.get_object()
        # 获取序列化对象
        ser = self.get_serializer(book)

        return Response(ser.data)
1234567891011121314
#  urls.py 中的 url
url(r'books/(?P<pk>\d+)/$', views.BookInfoAPIView.as_view())
12
```

------

## 扩展类

Ⅲ . 五个扩展类

1. **ListModelMixin**: 快速实现列表视图，返回200状态码。
    该Mixin的list方法会对数据进行过滤和分页。

```
# 查询所有书籍的信息
class BookInfoAPIView(ListModelMixin, GenericAPIView):
    # 定义视图的查询集
    queryset = BookInfo.objects.all()
    # 指定视图对应的序列化器
    serializer_class = BookInfoModelSerializer

    def get(self, request):
        return self.list(request)
123456789
```

1. **CreateModelMixin**： 快速实现创建资源的视图，成功返回201状态码。
    如果序列化器对前端发送的数据验证失败，返回400错误。

```
#  创建数据 
class BookInfoAPIView(CreateModelMixin, GenericAPIView):
    # 定义视图的查询集
    queryset = BookInfo.objects.all()
    # 指定视图对应的序列化器
    serializer_class = BookInfoModelSerializer

    def post(self, request):
        return self.create(request)
123456789
# urls.py
url(r'books/', views.BookInfoAPIView.as_view())
12
```

1. **RetrieveModelMixin**：  可以快速实现返回一个存在的数据对象。
    如果存在，返回200， 否则返回404。

```
class BookInfoAPIView(RetrieveModelMixin, GenericAPIView):
    # 定义视图的查询集
    queryset = BookInfo.objects.all()
    # 指定视图对应的序列化器
    serializer_class = BookInfoModelSerializer

    def get(self, request, pk):
        return self.retrieve(request)

123456789
# urls.py
url(r'books/(?P<pk>\d+)/$', views.BookInfoAPIView.as_view())
12
```

1. **UpdateModelMixin**:  更新视图扩展类，提供update(request, *args, **kwargs)方法，可以快速实现更新一个存在的数据对象。
    同时也提供partial_update(request, *args, **kwargs)方法，可以实现局部更新。
    成功返回200，序列化器校验数据失败时，返回400错误。

```
class BookInfoAPIView(UpdateModelMixin, GenericAPIView):
    # 定义视图的查询集
    queryset = BookInfo.objects.all()
    # 指定视图对应的序列化器
    serializer_class = BookInfoModelSerializer

    def put(self, request, pk):
        book = self.get_object()

        return self.update(request, book)
12345678910
```

1. **DestroyModelMixin**:  删除视图扩展类，提供destroy(request, *args, **kwargs)方法，可以快速实现删除一个存在的数据对象。
    成功返回204，不存在返回404。

------

## 视图集

Ⅳ . ViewSet 视图集

**常用视图集的父类**：

1） ViewSet
 继承自APIView，作用也与APIView基本类似，提供了身份认证、权限校验、流量管理等。

在ViewSet中，没有提供任何动作action方法，需要我们自己实现action方法。

2）GenericViewSet
 继承自GenericAPIView，作用也与GenericAPIVIew类似，提供了get_object、get_queryset等方法便于列表视图与详情信息视图的开发。

3）**ModelViewSet**
 继承自GenericAPIVIew，同时包括了ListModelMixin、RetrieveModelMixin、CreateModelMixin、UpdateModelMixin、DestoryModelMixin。

4）**ReadOnlyModelViewSet**
 继承自GenericAPIVIew，同时包括了ListModelMixin、RetrieveModelMixin

**action属性**：
 比如：

> urlpatterns = [
>  url(r’^books/ $ ‘, views.BookInfoViewSet.as_view({‘get’: ‘list’})),
>  url(r’^books/latest/$ ‘, views.BookInfoViewSet.as_view({‘get’: ‘latest’})),
>  url(r’^books/(?P\d+)/$ ‘, views.BookInfoViewSet.as_view({‘get’: ‘retrieve’})),
>  url(r’^books/(?P\d+)/read/$’ , views.BookInfoViewSet.as_view({‘put’: ‘read’})),
>  ]

在视图集中，我们可以通过action对象属性来获取当前请求视图集时的action动作是哪个

现在以**ModelViewSet** 做代码案例演示：

```
class BookInfoAPIView(ModelViewSet):
    # 定义视图的查询集
    queryset = BookInfo.objects.all()
    # 指定视图对应的序列化器
    serializer_class = BookInfoModelSerializer
12345
# url.py中的urlatterns配置
url(r'books/(?P<pk>\d+)/$', views.BookInfoAPIView.as_view({'get': 'retrieve'})),
url(r'books/', views.BookInfoAPIView.as_view({'get': 'list'}))
123
```

在继承了ModelViewSet之后，我们只要在路由中配置的时候，说明什么请求对应什么action，就可以很简单的完成视图函数的关于数据库的简单的增删改查。

------

Ⅴ . 路由router

上面的方式定义路由，相对来说，还是比较繁琐的，我们现在可以使用更简单的。

REST framework提供了两个router：

- SimpleRouter
- DefaultRouter

DefaultRouter与SimpleRouter的区别是，DefaultRouter会多附带一个默认的API根视图，返回一个包含所有列表视图的超链接响应数据。

**使用方法：**

（1）：创建router对象，并注册视图集

```
from rest_framework import routers

router = routers.SimpleRouter()
router.register(r'books', BookInfoAPIView, base_name='book')
1234
```

register(prefix, viewset, base_name)

- prefix 该视图集的路由前缀
- viewset 视图集
- base_name 路由名称的前缀

（2）：添加路由数据到urlpatterns中
 有下面两种方式都可以

```
urlpatterns = [
    ...
]
urlpatterns += router.urls
1234
urlpatterns = [
    ...
    url(r'^', include(router.urls))
]
1234
```

**路由router形成URL的方式**：
 ![在这里插入图片描述](http://www.pianshen.com/images/978/589ec9d5dfbd6d34e3b83b2d0b70908a.png)

![在这里插入图片描述](http://www.pianshen.com/images/894/419ebf4edcc546969c2df6d56c25a51e.png)

------

Ⅵ . 视图集中定义附加action动作

虽然ModelViewSet给我们提供了很简单的定义视图函数的方法，但是，并不能满足咱们的所有需求，所以我们得需要自己定义action动作。

添加自定义动作需要使用`rest_framework.decorators.action`装饰器。

以action装饰器装饰的方法名会作为action动作名，与list、retrieve等同。

action装饰器可以接收两个参数：

1. methods: 该action支持的请求方式，列表传递
2. detail: 表示是action中要处理的是否是视图资源的对象（即是否通过url路径获取主键）

detail参数：

-  True 表示使用通过URL获取的主键对应的数据对象
-  False 表示不使用URL获取主键

```
class BookInfoAPIView(ModelViewSet):
    # 定义视图的查询集
    queryset = BookInfo.objects.all()
    # 指定视图对应的序列化器
    serializer_class = BookInfoModelSerializer

    # 自定义action动作
    @action(methods=['get'], detail=False)
    def latest(self, request):
        # 本action是获取最新的书
        book = BookInfo.objects.latest('id')
        ser = self.get_serializer(book)
        return Response(ser.data)
12345678910111213
```

------

## 视图关系一览

​	Ⅶ. 视图函数继承关系

这个是从pycharm直接导出的，可以根据图示将视图之间的继承关系梳理清楚。
 ![在这里插入图片描述](http://www.pianshen.com/images/25/02c9070bed4f4c345dd88f746fa9b001.png)

原文地址：![img](http://www.pianshen.com/source?id=843087910)



