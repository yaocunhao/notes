#  一、Django如何处理请求

## 1.1 如何寻找路由路径

- [gd](https://docs.djangoproject.com/en/2.0/topics/http/urls/)
- Django 根据 ROOT_URLCONF设置的值来确定**根路由**
- 如果传入的HttpRequests 对象具有urlconf属性(由中间件设置)，则替代ROOT_URLCONF
- Django 在加载模块时，查找变量**urlpatterns**,它是 [`django.urls.path()`](https://docs.djangoproject.com/en/2.0/ref/urls/#django.urls.path)  或者 [`django.urls.re_path()`](https://docs.djangoproject.com/en/2.0/ref/urls/#django.urls.re_path) 的返回值
-  Django按顺序遍历每个URL，并在第一个匹配请求URL的模式处停止。

## 1.2 path、url、re_path 区别

- url 是Django老版本(2.0 之前)的函数，也就是re_path 的别名
- path 不支持正则表达式，re_path 支持正则表达式

# 二、类视图

- [gd](https://docs.djangoproject.com/en/2.0/ref/class-based-views/base/#django.views.generic.base.View)

- 所有的类视图都继承于 `django.views.View`

- View 类提供了如下三个方法

  - `dispatch(request,*args, **kwargs)`

    - 默认实现时检查http的请求方法，调用对应的接口
      - 比如：`GET` 将会去调用get接口， POST将会去调用post()接口
    - 默认情况下request的`HEAD`会被传递给`get()`，如果想要单独处理，可以重定义`head()`接口![image-20220831191030594](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202208311910841.png)

  - `http_method_not_allowed(request,*args, **kwargs)`

    - 当请求方法不被`HTTP`支持时，会调用该函数，返回一个异常(HttpResponseNotAllowed)

  - options

    - 返回允许的http方法，也就是`http_method_names `列表的值

  - as_view

    - 返回一个接受请求，并返回响应的可调用视图**view**：`response = MyView.as_view()(request)`

    - 返回的view有

      - 三个参数：`request, *args, **kwargs`
      - 两种属性：`view_clss`、`view_initkwargs`

    - 当view函数被调用时，`HttpRequest`会将request属性传递进去，其余的参数传递给`args`和`kwargs`，然后调用dispath函数

      

- View 类提供的属性

  - http_method_names = ['get', 'post', 'put', 'patch', 'delete', 'head', 'options', 'trace']
  - 表示默认该视图接受的HTTP方法名称列表

- 示例

  ```python
  # 类视图
  from django.http import HttpResponse
  from django.views import View
  
  class MyView(View):
  
      def get(self, request, *args, **kwargs):
          return HttpResponse('Hello, World!')
          
          
  # url
  from django.urls import path
  from myapp.views import MyView
  
  urlpatterns = [
      path('mine/', MyView.as_view(), name='my-view'), #  MyView.as_view()替代传统的视图函数
  ]
  ```

![image-20220901102708497](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202209011027869.png)