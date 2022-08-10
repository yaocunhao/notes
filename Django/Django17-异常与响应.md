# 一、响应

- [图绘链接](https://boardmix.cn/app/editor/Q7qiXZBDJiDN0dpsJiQ04g)

## 1.1 整体逻辑

![image-20220810155534792](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202208101555125.png)

## 1.2 JsonResponse 分析

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

  