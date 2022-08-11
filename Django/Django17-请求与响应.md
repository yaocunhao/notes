[参考链接](https://blog.csdn.net/zy010101/article/details/121030001?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522166013386716781818787080%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fall.%2522%257D&request_id=166013386716781818787080&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~pc_rank_34-1-121030001-null-null.142^v40^pc_rank_34_2,185^v2^control&utm_term=Django%20Http%20%E5%93%8D%E5%BA%94%E5%85%81%E8%AE%B8%E8%BF%94%E5%9B%9E%E6%80%8E%E4%B9%88%E7%9A%84%E5%86%85%E5%AE%B9&spm=1018.2226.3001.4187)

# 一、请求

- Django会自动创建HttpRequest(wsgi或者asgi创建)对象， HttpResponse则是后端开发人员负责实例化、填充和返回。**每一个视图函数都必须返回一个HttpResponse对象**
- HttpResponse类位于django.http模块中

# 二、响应

- [图绘链接](https://boardmix.cn/app/editor/Q7qiXZBDJiDN0dpsJiQ04g)

## 2.1 整体逻辑

- **注意:**Django的http请求，必须通过Django的`HttpResponse`这个类进行响应(Flask 框架则不需要固定的响应类来进行返回)
- Django 会自动将这个类解析成Json格式进行返回

![image-20220810200509950](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202208102005414.png)

## 2.2 JsonResponse 分析

- 作用
  - 将传入的`dict`数据序列化成`Json`格式

- 源码分析

  ```python
  class JsonResponse(HttpResponse):
      
      """
        该类来自django.http
        作用：将传入的data数据序列化成Json数据
        参数：
          data: dict类型
          safe：True，参数只可以是data类型，否则会出现安全问题
      """  
  
      def __init__(self, data, encoder=DjangoJSONEncoder, safe=True,
                   json_dumps_params=None, **kwargs):
          if safe and not isinstance(data, dict):
              raise TypeError(
                  'In order to allow non-dict objects to be serialized set the '
                  'safe parameter to False.'
              )
          if json_dumps_params is None:
              json_dumps_params = {}
          kwargs.setdefault('content_type', 'application/json')
          data = json.dumps(data, cls=encoder, **json_dumps_params)
          super().__init__(content=data, **kwargs)
  ```

  