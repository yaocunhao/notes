# 一、[django类视图as_view()方法解析](https://www.cnblogs.com/olivertian/p/11072528.html)

- 使用视图函数时，django完成URL解析之后，会直接把request对象以及URL解析器捕获的参数（比如re_path中正则表达捕获的位置参数或关键字参数）丢给视图函数，但是在类视图中，这些参数不能直接丢给一个类，所以就有了as_view方法，这个方法只做一件事就是返回一个闭包，这个闭包像视图函数一样接收url解析器传送过来的参数

# 二、DjangoURL调度

- Django如何处理请求

  - Django 确定要使用的根 URLconf 模块。通常，这是[`ROOT_URLCONF`](https://docs.djangoproject.com/en/1.11/ref/settings/#std:setting-ROOT_URLCONF)设置的值，但如果传入 `HttpRequest`对象具有[`urlconf`](https://docs.djangoproject.com/en/1.11/ref/request-response/#django.http.HttpRequest.urlconf) 属性（由中间件设置），则将使用其值代替 [`ROOT_URLCONF`](https://docs.djangoproject.com/en/1.11/ref/settings/#std:setting-ROOT_URLCONF)设置。(**ROOT_URLCONG在更目录的setting的之中**)

  - Django 加载该 Python 模块并查找变量 `urlpatterns`. 这应该是一个 Python[`django.conf.urls.url()`](https://docs.djangoproject.com/en/1.11/ref/urls/#django.conf.urls.url) 实例列表。

  - Django 按顺序遍历每个 URL 模式，并在与请求的 URL 匹配的**第一个模式处停止,URL必须以/结尾**。

  - 一旦其中一个正则表达式匹配，Django 就会导入并调用给定的视图，这是一个简单的 Python 函数（或基于

    类的视图

    ）。视图传递了以下参数：

    - 的一个实例[`HttpRequest`](https://docs.djangoproject.com/en/1.11/ref/request-response/#django.http.HttpRequest)。
    - 如果匹配的正则表达式没有返回命名组，则正则表达式中的匹配项作为位置参数提供。
    - 关键字参数由正则表达式匹配的任何命名组组成，被可选 `kwargs`参数中指定的任何参数覆盖[`django.conf.urls.url()`](https://docs.djangoproject.com/en/1.11/ref/urls/#django.conf.urls.url)。

  - 如果没有匹配的正则表达式，或者在此过程中的任何时候引发异常，Django 调用适当的错误处理视图。请参阅下面的[错误处理](https://docs.djangoproject.com/en/1.11/topics/http/urls/#error-handling)。

- 使用*命名*的 正则表达式组来捕获 URL 位并将它们作为*关键字* 参数传递给视图

  - 名正则表达式组的语法是`(?P<name>pattern)`，其中`name`是组的名称， `pattern`是要匹配的某种模式

```python
# 使用普通的方式
from django.conf.urls import url
from . import views
urlpatterns = [
    url(r'^articles/2003/$', views.special_case_2003),
    url(r'^articles/([0-9]{4})/$', views.year_archive),
    url(r'^articles/([0-9]{4})/([0-9]{2})/$', views.month_archive),
    url(r'^articles/([0-9]{4})/([0-9]{2})/([0-9]+)/$', views.article_detail),
]

# 使用命名组
urlpatterns = [
    url(r'^articles/2003/$', views.special_case_2003),
    url(r'^articles/(?P<year>[0-9]{4})/$', views.year_archive),
    url(r'^articles/(?P<year>[0-9]{4})/(?P<month>[0-9]{2})/$', views.month_archive),
    url(r'^articles/(?P<year>[0-9]{4})/(?P<month>[0-9]{2})/(?P<day>[0-9]{2})/$', views.article_detail),
]

# 使用命名组的优点的是，进行调用时，传参会带上 year=2001，而不是直接固定位置传参，这就以为着我们可以改变传参的固定位置，并且可以使参数更加的清晰
```



# 三、as_view() 方法分析

- 作用：[django](https://so.csdn.net/so/search?q=django&spm=1001.2101.3001.7020)的类视图拥有**自动查找指定方法**的功能，通过调用as_views()方法实现

## 3.1 一般的设计方法

- 一般的判断请求的设计方法

  - 获取当前的请求方法 `request.methon.lower`

  ```python
  def View(request,*args,**kwargs):
  	if request.method.lower() == 'get':
  		do_something()
  	if request.method.lower() == 'post':
  		do_something()
  ```

## 3.2 视图类设计方法

- 编写自定义的视图类，前提必须要继承基类View，然后使用View.as_view()代替判断:

  ```python
  class ClassName(View):
      '''
      继承View自动判断请求方法
      '''
      def post(): # post方法
          pass
  
      def get(): # get()方法
          pass
  
      def other(): # 其它方法
          pass
  
  #调用方法 -> 在项目之中看到的，url后面不是视图函数，而是使用类名去调用继承下来的as_view()这个函数
  url(url, ClassName.as_view(), name)
  ```

- **设计思想：**

  - 把视图函数的逻辑定义到类的方法里面去，然后在函数中实例化这个类，通过调用类的方法实现函数逻辑
  - 而把逻辑定义在类中的一个好处就是可以**通过继承复用这些方法**

- as_view()这个方法是如何执行对应自定义的get或者post方法呢

  - 让类视图生效的基本格式

    ```python
    # urls.py
    from .views import IndexView  # 导入自定义的视图类
    
    urlpatterns = [
    	url(r^'^$',IndexView.as_view(),name='index')
    ]
    
    ```

    - Django 使用如上的方式配置 URL 到对应视图函数的路由映射
    - 注意到 url() 函数前两个位置参数需要传递的值，第一个是需要捕获的 url 的正则模式，第二个参数则是一个可调用的对象（即视图函数）
    - 如果我们通过 def 定义视图函数，那么传入的这个可调用对象就是这个函数本身；**而如果我们定义的是类视图，则必须调用类视图的 as_view 方法返回一个根据这个类生成的可调用对象**

  - 上面提到需要根据类视图的as_view方法返回一个根据这个类生成的可调用对象，下面看一看Django是如何将一个类转变成函数的

    ```python
    @classonlymethod #修饰器->类函数
        def as_view(cls, **initkwargs):
            """Main entry point for a request-response process."""
          	
            # 第一个for循环
            # 1、防止传入get、post这样的参数将类本身的参数给覆盖了
            # 2、防止传入类为定义的参数
            for key in initkwargs:
                if key in cls.http_method_names:
                    raise TypeError("You tried to pass in the %s method name as a "
                                    "keyword argument to %s(). Don't do that."
                                    % (key, cls.__name__))
                if not hasattr(cls, key):
                    raise TypeError("%s() received an invalid keyword %r. as_view "
                                    "only accepts arguments that are already "
                                    "attributes of the class." % (cls.__name__, key))
    				
            # 这个函数就是视图函数的标准定义
            # 接收一个request对象，以及从url捕获的命名组/非命名组参数
            # hasattr函数，判断一个类之中是否有该属性
            def view(request, *args, **kwargs):
                self = cls(**initkwargs) # 实例化一个类视图对象
                
                #如果这个类之中有get属性 && 没有head属性，那么就进行设置
                if hasattr(self, 'get') and not hasattr(self, 'head'):
                    self.head = self.get  # 标识默认使用get方法
                
                # 将函数的参数设置为类视图的实例属性
                self.request = request
                self.args = args
                self.kwargs = kwargs
                
                # 返回视图函数被要求返回的HttpResponse对象
                # dispatch 方法会根据 HTTP 请求方法的不同去调用对应的处理方法
                return self.dispatch(request, *args, **kwargs)
              
              
            view.view_class = cls
            view.view_initkwargs = initkwargs
    
            # take name and docstring from class
            update_wrapper(view, cls, updated=())
    
            # and possible attributes set by decorators
            # like csrf_exempt from dispatch
            update_wrapper(view, cls.dispatch, assigned=())
            return view
    ```

    - as_view 方法被调用时允许传递一些关键字参数，不过需要做一个点点检查，第一防止你传入诸如 get、post 这样的关键字参数把类本身的 get、post 方法覆盖了；第二是防止你传入未定义为类属性的参数。最开始的 for 循环就是做这个事
    - 接下来在 as_view 方法中又定义了一个 view 方法，这个方法相信如果你经常写视图函数的话应该非常眼熟，这就是视图函数的标准定义
      - 接收一个 HttpRequest 对象，以及从 url 捕获的非命名组和命名组参数
      - 只不过在 view 这个视图函数里还多做了一点事，它首先实例化了一个类视图对象，然后把函数的参数设置为了这个类视图实例的属性
      - 接着便调用了实例的 dispatch 方法返回视图函数被要求返回的 HttpResponse 对象（注意 dispatch 方法会根据 HTTP 请求方法的不同去调用对应的处理方法）。接着把类中的一些文档字符串和函数名等更新到定义的 view 函数中，然后 as_view 方法返回这个 view 函数。

  - 调用顺序

    - as_view --> view --> dispatch
    - 以看出as_view实际上是一个闭包，他的作用就是做一些检验工作，再返回view方法
    - 而view方法的作用是给请求对象补充三个参数，并调用dispatch方法处理
    - dispatch方法查找到指定的请求方法，并执行相应代码块
    - **可以得出结论：as_view方法实际上最后就是要调用dispatch方法**

- 回过头来再看一下我们的 url 模式定义：

  ```python
  urlpatterns = [
  	url(r^'^$',IndexView.as_view(),name='index')
  ]
  ```

  - views.IndexView.as_view() 调用后返回的就是一个在 IndexView 里通过 def 定义的视图函数 view（注意所有类视图都继承自 View 基类），是不是和你直接在这里放一个视图函数是一样的？

- 总结

  - 现在我们已经明白了类视图的基本结构，其主要功能就是根据 HTTP 请求方法的不同做出相应处理，具体的实现为 dispatch 方法。类视图的核心思想就是把视图函数的逻辑定义到类的方法里面去，然后在函数中实例化这个类，通过调用类的方法实现函数逻辑

# 四、Django之request介绍

[参考链接](https://www.cnblogs.com/shangping/p/11602885.html)

## 4.1  是什么

- 当一个页面被请求时，Django就会**创建**一个包含本次请求原信息（请求报文中的请求行、首部信息、内容主体等）的**HttpRequest对象**。**Django会将这个对象自动传递给响应的视图函数，一般视图函数约定俗成地使用request参数承接这个对象**

  ```python
  func(request):
  pass
  ```

## 4.2 可调用的方法

-  path_info 返回用户访问url,不包括域名
- method 请求中使用的HTTP方法的字符串表示,全大写表示
- GET 包含所有HTTP GET参数的类字典对象
- POST 包含所有HTTP POST参数的类字典对象
- body 请求体,byte类型 request.POST的数据就是从body里面提取到的

## 4.3 属性说明

- 所有的属性应该被认为是只读的，除非另有说明
- django将请求报文中的请求行、头部信息、内容主体封装成 HttpRequest 类中的属性。
  除了特殊说明的之外，其他均为只读的

# 五、使用redis作为缓存

- [链接](https://blog.csdn.net/weixin_42134789/article/details/102386456)

## 5.1 配置

- settings.py中加入以下内容配置缓存![image-20220314213552680](../../Library/Application Support/typora-user-images/image-20220314213552680.png)

## 5.2 使用缓存的几种方式

- 视图中使用缓存

  - **用cache_page 修饰视图函数**

  - 下面这段代码将my_view这个视图函数缓存60*15秒，即15分钟，这个视图中所有的url都会创建一个缓存。

    ```python
    from django.views.decorators.cache import cache_page
    
    @cache_page(60 * 15) 
    def my_view(request):
      return render(request, 'index.html')
    ```

    - 但是，需要说明的是，给视图添加缓存是有风险的，如果视图所展示的网页中有经常动态变动的信息，那么被添加缓存命令不可取
    - 缓存整个视图最实用的场景应该是这个视图所展示的网页的内容基本上不怎么变动，或者说在很长一段时间内不需要变动，这样使用缓存就非常有效

- URLconf中使用缓存

  - 上面说了函数视图使用缓存，但是我们可能还有一种场景，那就是多个 URL 指向同一个函数视图，但是我只想缓存一部分的 URL，这时候就可以采用在 URLconf 中使用缓存，这样就指定了哪些 URL 需要缓存。

  - 下面分别表示了函数视图和类视图的路由中使用缓存的方式，基本一致：

    ```python
    from django.views.decorators.cache import cache _page
    
    urlpatterns = [
    
      url(r'^foo/([0-9]{1,2})/$',cache_page(60 * 15)(my_view)), #缓存一部分url
    
      url(r'^$', cache_page(60 * 30)(IndexView.as_view()), name='index'),
    
    ]
    ```

  - URLconf 使用缓存和视图函数使用缓存需要注意的地方是一样的，因为它们都是缓存整个页面，所有都需要考虑是否整个页面都应该缓存

- 函数中使用缓存

  - 函数中使用缓存是最基本的使用方法，跟在其他非 django 中使用的方式一致，无非就是使用 set() 和 get() 方法。例如我有一个使用场景：我的博客的文章是使用的 markdown 的格式输入的，所以每次展现到前端之前后端都需要把文章的内容进行一次 markdown 转化，这个渲染的过程难免会有点影响性能，所以我可以使用缓存来存放已经被渲染过的文章内容。具体的代码片段如下：

    ```python
    ud = obj.update_date.strftime("%Y%m%d%H%M%S")
    
    md_key = '{}_md_{}'.format(obj.id, ud) #创建一个缓存key
    
    cache_md = cache.get(md_key) #判断是否读取缓存
    
    if cache_md:
    
      md = cache_md #进行缓存的读取
    
    else:
    
      md = markdown.Markdown(extensions=[
    
        'markdown.extensions.extra',
    
       'markdown.extensions.codehilite',
    
       TocExtension(slugify=slugify),
    
      ])
    
      cache.set(md_key, md, 60 * 60 * 12) #进行缓存
    ```

    - 上面的代码中，我选择文章的 ID 和文章更新的日期作为缓存的 key，这样可以保证当文章更改的时候能够丢弃旧的缓存进而使用新的缓存，而当文章没有更新的时候，缓存可以一直被调用，直到缓存按照设置的过期时间过期。

- 模板中使用缓存

  - 模板中使用缓存是我比较推荐的一种缓存方式，因为使用这种方式可以充分的考虑缓存的颗粒度，细分颗粒度，可以保证只缓存那些适合使用缓存的 HTML 片段

  - 具体的使用方式如下，首先加载 cache 过滤器，然后使用模板标签语法把需要缓存的片段包围起来即可

    ```python
    {% load cache %}
    
    {% cache 500 ‘cache_name’ %}
    
        <div></div>
    
    {% endcache %}
    ```

- 总结

  - 先说一下我在使用缓存的时候遇到的问题，我之前给我的很多视图函数还有URL路由添加了缓存，也就是缓存整个页面，后来发现出问题了，因为我的每个页面都有导航栏，而导航栏上面有登录和登出按钮，这样如果缓存起来的话，就无法让用户显示登录和登出了，并且，有表单的页面也无法提交表单，总之，缓存整个页面是一件有风险的行为。

  

# 五、静态资源的存放

- [参考链接](https://blog.csdn.net/qq_24551305/article/details/84865025)

- 在static标签中引用文件时有两个查找路径：1、app下的static。2、工程下的commen_static（STATICFILES_DIRS)

  STATICFILES_DIRS告诉django,首先到STATICFILES_DIRS里面寻找静态文件,其次再到各个app的static文件夹里面找(注意，django查找静态文件是惰性查找，查找到第一个，就停止查找了)



# 六、 数据库操作方法

- get方法：匹配到数据时返回一个对象，可以对查询到的数据进行修改(含有.save()方法)。没有匹配到数据时会报错
- filter方法：匹配到数据时返回一个列表，不可以对查询到的数据进行修改(没有.save()方法)。没有匹配到数据时会返回一个空列表[].

# 七、脚本运行方法

- 需要使用 --script-args 命令来进行传参

[文档](https://django-extensions.readthedocs.io/en/latest/runscript.html)

- 脚本可以放在项目或者App的script目录之下
- **如果寻找不到，则说明路径太深，则需要带上路径**



# 八、创建models文件夹、删除迁移文件目录后再修改

[参考链接](https://blog.csdn.net/weixin_30389003/article/details/97545000?ops_request_misc=&request_id=&biz_id=102&utm_term=%E5%B0%86Django%E9%A1%B9%E7%9B%AE%E4%B8%8B%E7%9A%84models%E6%96%87%E4%BB%B6%E6%94%B9%E6%88%90%E6%96%87%E4%BB%B6%E5%A4%B9&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-1-97545000.142^v21^pc_rank_34,157^v15^new_3&spm=1018.2226.3001.4187)
