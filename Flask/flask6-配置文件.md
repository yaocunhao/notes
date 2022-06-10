# 一、配置文件方法

[参考链接1](https://blog.csdn.net/qq_39813400/article/details/111625545?ops_request_misc=&request_id=&biz_id=102&utm_term=Flask%20%E9%85%8D%E7%BD%AE&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-1-111625545.142^v11^pc_search_result_control_group,157^v13^control&spm=1018.2226.3001.4187)

[参考2](https://blog.csdn.net/qq_42684307/article/details/81048652?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522165467319016782350918368%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=165467319016782350918368&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-2-81048652-null-null.142^v11^pc_search_result_control_group,157^v13^control&utm_term=app.config.from_envvar&spm=1018.2226.3001.4187)

## 1.1 方法一

- `app.config`本质上就是一字典，因此可以直接用操作字典的方式进行赋值

  ```python
  app.config['DEBUG'] = True
      PS： 由于Config对象本质上是字典，所以还可以使用app.config.update(...)
  ```

  

## 1.2 方法二

- 从文件中读取配置

  ```python
  app.config.from_pyfile("python文件名称") # 从配置文件中加载
  
  # 从环境变量加载，环境变量的值为python文件名称，内部调用from_pyfile方法，本质还是从文件加载
  app.config.from_envvar("环境变量名称")
  
  app.config.from_json("json文件名称") # JSON文件名称，必须是json格式，因为内部会执行json.loads
  
  app.config.from_mapping({'DEBUG':True}) # 字典格式，这种第一种本质上是一样的
  
  app.config.from_object("python类或类的路径") # 从配置对象加载，如下所示就是加载类的成员
    
  # 配置对象，里面定义需要给 APP 添加的一系列配置
  class Config(object):
      DEBUG = True
  
  # 从配置对象中加载配置
  app.config.from_object(Config) 
  ```

# 二、默认的参数配置

```python
# 常用配置



{
    'DEBUG': False,  # 是否开启Debug模式 如果代码有修改随时自动重启
    'TESTING': False,  # 是否开启测试模式
    'SQLALCHEMY_COMMIT_ON_TEARDOWN':True，#因为安全问题，设置无效。
    ' SQLALCHEMY_TRACK_MODIFICATIONS ':True # 设置sqlalchemy跟踪对象的修改并发出信号,占用内存，有需要时再开启
    'PROPAGATE_EXCEPTIONS': None,  # 异常传播(是否在控制台打印LOG) 当Debug或者testing开启后,自动为True
    'PRESERVE_CONTEXT_ON_EXCEPTION': None,  # 一两句话说不清楚,一般不用它
    'SECRET_KEY': None,  # 之前遇到过,在启用Session的时候,一定要有它，不涉及登录session ，设置没用
    'PERMANENT_SESSION_LIFETIME': 31,  # days , Session的生命周期(天)默认31天
    'USE_X_SENDFILE': False,  # 是否弃用 x_sendfile
    'LOGGER_NAME': None,  # 日志记录器的名称
    'LOGGER_HANDLER_POLICY': 'always',
    'SERVER_NAME': None,  # 服务访问域名
    'APPLICATION_ROOT': None,  # 项目的完整路径
    'SESSION_COOKIE_NAME': 'session',  # 在cookies中存放session加密字符串的名字
    'SESSION_COOKIE_DOMAIN': None,  # 在哪个域名下会产生session记录在cookies中
    'SESSION_COOKIE_PATH': None,  # cookies的路径
    'SESSION_COOKIE_HTTPONLY': True,  # 控制 cookie 是否应被设置 httponly 的标志，
    'SESSION_COOKIE_SECURE': False,  # 控制 cookie 是否应被设置安全标志
    'SESSION_REFRESH_EACH_REQUEST': True,  # 这个标志控制永久会话如何刷新
    'MAX_CONTENT_LENGTH': None,  # 如果设置为字节数， Flask 会拒绝内容长度大于此值的请求进入，并返回一个 413 状态码
    'SEND_FILE_MAX_AGE_DEFAULT': 12,  # hours 默认缓存控制的最大期限
    'TRAP_BAD_REQUEST_ERRORS': False,
    # 如果这个值被设置为 True ，Flask不会执行 HTTP 异常的错误处理，而是像对待其它异常一样，
    # 通过异常栈让它冒泡地抛出。这对于需要找出 HTTP 异常源头的可怕调试情形是有用的。
    'TRAP_HTTP_EXCEPTIONS': False,
    # Werkzeug 处理请求中的特定数据的内部数据结构会抛出同样也是“错误的请求”异常的特殊的 key errors 。
    # 同样地，为了保持一致，许多操作可以显式地抛出 BadRequest 异常。
    # 因为在调试中，你希望准确地找出异常的原因，这个设置用于在这些情形下调试。
    # 如果这个值被设置为 True ，你只会得到常规的回溯。
    'EXPLAIN_TEMPLATE_LOADING': False,
    'PREFERRED_URL_SCHEME': 'http',  # 生成URL的时候如果没有可用的 URL 模式话将使用这个值
    'JSON_AS_ASCII': True,打印了的中文会转义，建议改成flase
    # 默认情况下 Flask 使用 ascii 编码来序列化对象。如果这个值被设置为 False ，
    # Flask不会将其编码为 ASCII，并且按原样输出，返回它的 unicode 字符串。
    # 比如 jsonfiy 会自动地采用 utf-8 来编码它然后才进行传输。
    'JSON_SORT_KEYS': True,
    #默认情况下 Flask 按照 JSON 对象的键的顺序来序来序列化它。
    # 这样做是为了确保键的顺序不会受到字典的哈希种子的影响，从而返回的值每次都是一致的，不会造成无用的额外 HTTP 缓存。
    # 你可以通过修改这个配置的值来覆盖默认的操作。但这是不被推荐的做法因为这个默认的行为可能会给你在性能的代价上带来改善。
    'JSONIFY_PRETTYPRINT_REGULAR': True,
    'JSONIFY_MIMETYPE': 'application/json',
    'TEMPLATES_AUTO_RELOAD': None,
}```


```

