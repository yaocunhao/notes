[参考链接](https://blog.csdn.net/perfectsorrow/article/details/80237066?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522166011162316782388074014%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=166011162316782388074014&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-1-80237066-null-null.142^v40^pc_rank_34_2,185^v2^control&utm_term=werkzeug&spm=1018.2226.3001.4187)

# 一、是什么

- Werkzeug是一个[WSGI](https://so.csdn.net/so/search?q=WSGI&spm=1001.2101.3001.7020)工具包，他可以作为一个Web框架的底层库。这里稍微说一下， werkzeug 不是一个web服务器，也不是一个web框架，而是一个工具包，官方的介绍说是一个 WSGI 工具包，它可以作为一个 Web 框架的底层库，因为它封装好了很多 Web 框架的东西，例如 Request，Response 等等

# 二、应用

## 2.1 使用介绍

- 这个包在Flask之中，如果直接返回对应的对象，则会根据对象建造一个表单(**应该是wsgi协议规定的，读取的时候构造了一个html页面**)![image-20220810232749846](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202208102327027.png)

```python
from werkzeug.exceptions import HTTPException


class MapException(HTTPException):
  """base map exception class"""

  def __init__(self, code, msg):
    self.code = code
    super().__init__(msg)


from werkzeug.exceptions import HTTPException

m = MapException(500, '500 error!!')
print(m)  # 500 Internal Server Error: 500 error!!， 实际调用的是http包的str/repr 函数，这个函数根据初始化传入的参数返回内容
```

## 2.2 为什么需要继承使用

- 如果不继承使用的话，在打印消息的时候，重写的`__str__`方法会根据code获取对应的错误描述符。如果不继承，code是无法直接传给HTTPException的，所以需要继承使用

```python
from werkzeug.exceptions import HTTPException

class Http_test(HTTPException):
  def __init__(self, code, msg):
    self.code = code
    super().__init__(msg)  # 传入描述消息给Http异常父类

# 未继承
_h = HTTPException('123')
print(_h) # ??? Unknown Error: 123

# 继承使用
h = Http_test(code=200, msg='123')  # 200 OK: 123
print(h)  # 打印对象，实际调用的是__str__方法，如果不进行继承，那么父类是无法获取code的，也就无法根据code获取对应的描述信息
```

