[django官网](https://docs.djangoproject.com/en/2.0/intro/tutorial01/)

# 一、MVC模式

- MVC 是一种软件开发的方法，它把代码的定义和数据访问的方法（模型）与请求逻辑 （控制器）还有用户接口（视图）分开来
- 这种设计模式关键的优势在于各种组件都是 松散结合 的。这样，每个由 Django驱动 的Web应用都有着明确的目的，并且可独立更改而不影响到其它的部分。比如，开发者更改一个应用程序中的URL而不用影响到这个程序底层的实现。设计师可以改变 HTML 页面 的样式而不用接触 Python 代码。数据库管理员可以重新命名数据表并且只需更改一个地方，无需从一大堆文件中进行查找和替换

# 二、开始一个项目

## 2.1 django前序介绍

- 生成目录
  - `django-admin.py startproject mysite`
    - **__init__.py** ：让 Python 把该目录当成一个开发包 (即一组模块)所需的文件。 这是一个空文件，一般你不需要修改它。
    - **manage.py** ：一种命令行工具，允许你以多种方式与该 Django 项目进行交互。 键入python manage.py help，看一下它能做什么。 你应当不需要编辑这个文件；在这个目录下生成它纯是为了方便。
    - **settings.py** ：该 Django 项目的设置或配置。 查看并理解这个文件中可用的设置类型及其默认值。
    - **urls.py**：Django项目的URL设置。可视其为你的django网站的目录。 目前，它是空的。
    - `mysite/wsgi.py`: 为您的项目提供服务的 WSGI 兼容 Web 服务器的入口点
- 运行服务
  - 启动本地服务`python manage.py runserver`(默认是127.0.0.0：8000)
  - 启动网络服务`python manage.py runserver 0.0.0.0:8000`(也可以使用0来进行表示)

## 2.2 第一份视图

- Django对于`view.py`的文件命名没有特别的要求，它不在乎这个文件叫什么。但是根据约定，把它命名成`view.py`是个好主意，**这样有利于其他开发者读懂你的代码**

- 创建一个目录：`python manage.py startapp polls`

- 在`polls/views.py`文件下加入以下代码

  ```python
  from  django.http  import  HttpResponse 
  
  
  def  index ( request ): 
      return  HttpResponse ( "Hello, world. You're at the polls index." )
  ```

  - 首先，我们从 django.http 模块导入（import） HttpResponse 类
  - 接下来，我们定义一个叫做index的视图函数
  - 每个视图函数至少要有一个参数，通常被叫作`request`。 这是一个触发这个视图、包含当前Web请求信息的对象，是类`django.http.HttpRequest`的一个实例。在这个示例中，我们虽然不用`request`做任何事情，然而它仍必须是这个视图的第一个参数
  - 注意视图函数的名称并不重要；并不一定非得以某种特定的方式命名才能让 Django 识别它,比如不叫index也可以叫做hello
  - 这个函数只有简单的一行代码：它仅仅返回一个`HttpResponse`对象，这个对象包含了文本“Hello world”
  - 这里主要讲的是：一个视图就是Python的一个函数。这个函数第一个参数的类型是`HttpRequest`；它返回一个`HttpResponse`实例。为了使一个Python的函数成为一个Django可识别的视图，它必须满足这两个条件。（也有例外，但是我们稍后才会接触到。)

## 2.2 第一个URLconf

- 我们的`mysite`项目还对`hello`视图一无所知。我们需要通过一个详细描述的URL来显式的告诉它并且激活这个视图（相当于目前已经写好了HTML文件，但是并没有传到服务器目录上）为了绑定视图函数和URL，我们使用`URLconf`

- `URLconf` 就像是 Django 所支撑网站的目录。 它的本质是 URL 模式以及要为该 URL 模式调用的视图函数之间的映射表

  - 你就是以这种方式告诉Django，对于这个URL调用这段代码，对于那个URL调用那段代码。**也就是说，告诉Django资源所在地**

- 在`polls/urls.py`之中添加如下代码

  ```python
  from django.urls import path #导入django.urls模块下的path
  
  from . import views #导入当前模块的views
  
  urlpatterns = [
      path('', views.index, name='index'),
  ]
  ```

- 在项目URLconf之中包含当前的url：在`mysite/urls.py`写入以下代码

  ```python
  from django.contrib import admin
  from django.urls import include, path
  
  urlpatterns = [
      path('polls/', include('polls.urls')), #include函数，引用其它的URLconf
      path('admin/', admin.site.urls),
  ]
  ```

  - `urlpatterns` 变量， Django 期望能从 `ROOT_URLCONF` 模块中找到它。 该变量定义了 URL 以及用于处理这些 URL 的代码之间的映射关系

  - path之中传递了四个参数，两个是必须的两个是可选的`path(<route>, <view>, [name=None,\**kwargs]) `

    - 第一个参数route

      - route 表示路径，从端口以后的URL地址，到/结束
      - 当处理一个请求时，Django 从第一个模式开始`urlpatterns`并沿着列表向下移动，将请求的 URL 与每个模式进行比较，直到找到匹配的一个

    - 第二个参数view

      - view 必须是一个函数或者是引用其他的URLconfs(urls.py)即路由模块

        ```python
        如：
        
        　　urlpatterns = [
        
        　　  path('polls/',include('path.urls')), #引用其它的URL
        
        　　  path('admin/',admin.site.urls),
        
        　　]
        ```

      - 当 Django 找到匹配的模式时，它会调用指定的视图函数，并将[`HttpRequest`](https://docs.djangoproject.com/en/2.0/ref/request-response/#django.http.HttpRequest)对象作为第一个参数，并将路由中任何“捕获”的值作为关键字参数

    - 第三个参数name

      - 任意关键字参数可以在字典中传递给目标视图
      - **表示的是route匹配到的URL的一个别名**

    - 第四个参数kwargs

      - 命名你的 URL 可以让你从 Django 的其他地方明确地引用它，尤其是在模板中。这个强大的功能允许您对项目的 URL 模式进行全局更改，而只需触摸一个文件

- 在网址中输入ip:port/polls/ 就可以访问到内容了

## 2.3 流程分析

- 在游览器之中，敲如ip:prot/polls/来访问hello word消息时，django在后台的动作

  - 所有均开始于setting文件。当你运行`python manage.py` runserver，脚本将在于`manage.py`同一个目录下查找名为`setting.py`的文件。这个文件包含了所有有关这个Django项目的配置信息，均大写： TEMPLATE_DIRS，DATABASE_NAME ， 等。 最重要的设置时ROOT_URLCONF，它将作为URLconf告诉Django在这个站点中那些Python的模块将被用到

    ```
    ------在setting之中------
    ROOT_URLCONF = 'mysite.urls'
    
    
    ------在mysite.urls之中------
    from django.contrib import admin
    from django.urls import include, path
    
    urlpatterns = [
    	path('polls/', include('polls.urls')),
        path('admin/', admin.site.urls),
    ]
    ```

  - 当访问 URL /index/ 时，Django 根据 ROOT_URLCONF 的设置装载 URLconf 。然后按顺序逐个匹配URLconf里的URLpatterns，直到找到一个匹配的。当找到这个匹配的URLpatterns就调用相关联的view函数，并把 HttpRequest 对象作为第一个参数

  - 正如我们在第一个视图例子里面看到的，一个视图功能必须返回一个HttpResponse。 一旦做完，Django将完成Python的对象到一个合适的带有HTTP头和body的Web Response的过程（例如，python对象 -> 网页内容，**这个过程不需要我们管，这是框架带来的遍历**）

- 总结

  - 进来的请求转入`/index/`
  - Django通过在ROOT_URLCONF配置来决定根URLconf
  - Django在URLconf中的所有URL模式中，查找第一个匹配/index/的条目
  - 如果找到匹配，将调用相应的视图函数
  - 视图函数返回一个`HttpResponse`
  - Django转换HttpResponse为一个适合的HTTP response，以Web page显示出来

# 三、动态视图

## 3.1 动态内容

-   一个动态时钟视图。这个视图需要做两件事情： 计算当前日期和时间，并返回包含这些值的HttpResponse 

  ```python
  #-----如何动态的显示时钟----
  import datetime 
  now = datetime.datetime.now()
  print(now)
  #2022-03-08 21:49:06.391186
  ```

- 将这个显示时间的逻辑封装起来，包装视图`polls/view.py`之中

  ```python
  def index(request):
      return HttpResponse("Hello, world. You're at the polls index.")
  
  def GetTime(request):
  	now = datetime.datetime.now()
  	html = "<html><body>It is now %s.</body></html>" % now
  	return HttpResponse(html)
  ```

- 在`polls/urls.py之中填写URL和view的关系`

  ```python
  urlpatterns = [
      path('index/', views.index, name='index'),
      path('GetTime/', views.GetTime, name='GetTime'),
  ]
  ```

- 在mysite项目的`urls.py`之中填写路径关系

  ```python
  from django.contrib import admin
  from django.urls import include, path
  
  urlpatterns = [
  	path('polls/', include('polls.urls')),
    path('admin/', admin.site.urls),
  ]
  ```

- **值得一提的是url并不是代表路径，而是这样写更直观**

  - 在`polls/urls.py`之中，一个多个url可以对应一个视图函数,也就是说

    ```python
    urlpatterns = [
        path('index/', views.index, name='index'),
      
      #------对应同一个视图函数
        path('GetTime/', views.GetTime, name='GetTime'),
        path('time/', views.GetTime),
    ]
    ```

    

  - 在`mysite.urls`之中也可以将代码进行如下修改

    ```python
    urlpatterns = [
    	path('pollss/', include('polls.urls')),  ->pollss也不是路径
        path('admin/', admin.site.urls),
    ]
    ```

    

## 3.2 动态URL

- 描述

  - 在大多数动态web应用程序，URL通常都包含有相关的参数。举个例子，一家在线书店会为每一本书提供一个URL，如：/books/243/、/books/81196/

  - 现在对URL进行设计，比如

    - `/time/plus/1/`显示当前时间＋1个小时的页面
    - `/time/plus/2/` 显示当前时间＋2个小时的页面
    - `/time/plus/3/` 显示当前时间＋3个小时的页面，以此类推

  - 显然，我们可以配置多个URL然后对应同一个函数，但是这样会导致URL的冗余，因此可以使用通配符

    ```python
    (r'^time/plus/(\d{1,2})/$', hours_ahead)
    #r 表示使用元字符串，不将 / 转义
    # \d{1,2} 表示只允许两个数字，也就是最大为99
    # 想要将数字作为参数，因此用()将其括起来
    ```

- `polls/views`视图编写

  ```python
  def time(requese,offset):
  	try:
  		offset = int(offset)
  	except ValueError:
  		raise Http404('请输入数字')
  
  	now = datetime.datetime.now()
  	time = datetime.datetime.now() + datetime.timedelta(hours=offset) #时间+往后延时
  	html = "<html><body>当前时间:%s,往后延时:%s,延时后的时间:%s</body></html>" % (now,time,offset)
  	return HttpResponse(html)
  ```

- `polls/url编写`

  ```python
  from django.urls import path,re_path
  
  from . import views
  
  urlpatterns = [
      path('index/', views.index, name='index'),
      path('GetTime/', views.GetTime, name='GetTime'),
  	re_path(r'^time/plus/(\d{1,2})/$',views.time,name='time') #re_path使用正则表达式，想要将数字作为参数，用()括起来
  ]
  ```

- `mysite/urls编写`

  ```
  from django.contrib import admin
  from django.urls import include, path
  
  urlpatterns = [
  	path('polls/', include('polls.urls')),
      path('admin/', admin.site.urls),
  ]
  ```

- 效果![image-20220309113327146](../../Library/Application Support/typora-user-images/image-20220309113327146.png)

