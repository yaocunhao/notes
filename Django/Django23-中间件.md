- [gd](https://docs.djangoproject.com/en/2.0/topics/http/middleware/)

- 中间件的设置
  - 在下图之中添加路径模块即可(有系统自带的，有自定义的)![image-20220902005043448](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202209020050846.png)

- 如何自定义中间件

  - 在调用视图函数之前会调用中间件中的`**self.process_request(request)**`方法

  - 在执行完视图函数后会调用中间件中的另外一个方法``self.process_response(request, response)``

  - 所以定义中间件相当于加了一个装饰器

    ```python
    Django 提供django.utils.deprecation.MiddlewareMixin了轻松创建与MIDDLEWARE旧的MIDDLEWARE_CLASSES. Django 包含的所有中间件类都与这两种设置兼容。
    
    mixin 提供了一个__init__()方法，该方法接受一个可选 get_response参数并将其存储在self.get_response.
    
    __call__()方法：
    
    调用self.process_request(request)（如果已定义）。
    调用self.get_response(request)以从后面的中间件和视图中获取响应。
    调用（如果已定义）。self.process_response(request, response)
    返回响应。
    ```

    