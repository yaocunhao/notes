# 一、缓存系统使用流程

![image-20220507105118703](https://gitee.com/yao-cunhao/ssh_picture/raw/master/pict/202205071052204.png)

# 二、源码分析

## 2.1 caches

- 首先，django通过暴露给外边一个caches变量进行接口的使用

  ```python
  # 获取一个redis对象
  class CacheHandler:
      """
      A Cache Handler to manage access to Cache instances.
  
      Ensure only one instance of each alias exists per thread.
      """
      def __init__(self):
          self._caches = local() # 本地线程存储变量
  
      def __getitem__(self, alias):
          try:
              return self._caches.caches[alias] # 通过下标获取对应的redis
          except AttributeError:
              self._caches.caches = {} # 给本地线程赋值一个字典
          except KeyError:
              pass
  
          # if alias not in settings.CACHES:  # 如果下标不在setting之中，则报错处理
          #     raise InvalidCacheBackendError(
          #         "Could not find config for '%s' in settings.CACHES" % alias
          #     )
  
          cache = _create_cache(alias) # 创建了一个redis对象
          self._caches.caches[alias] = cache
          return cache
  
      def all(self):
          return getattr(self._caches, 'caches', {}).values()
  
  
  	# 对外暴露的接口
  caches = CacheHandler()
  ```

## 2.2 create_cache 

- 进行缓存对象的创建接口，这里值得学习的是如何通过settings文件进行内容的读取的

  ```python
  # 创建一个redis对象
  def _create_cache(backend, **kwargs):
      try:
          # Try to get the CACHES entry for the given backend name first
          try:
              conf = settings.CACHES[backend] # 从setting之中获取cache配置
          except KeyError:
              try:
                  # Trying to import the given backend, in case it's a dotted path
                  import_string(backend)
              except ImportError as e:
                  raise InvalidCacheBackendError("Could not find backend '%s': %s" % (
                      backend, e))
              location = kwargs.pop('LOCATION', '')
              params = kwargs
          else:
              params = conf.copy()
              params.update(kwargs) # 添加额外的参数
  
  						# 只剩下 OPTIONS "CLIENT_CLASS": "django_redis.client.DefaultClient"
              backend = params.pop('BACKEND') # "django_redis.cache.RedisCache"
              location = params.pop('LOCATION', '') # f'redis://{REDIS_HOST}:{REDIS_PORT}/{REDIS_DB_INDEX}'
  
          backend_cls = import_string(backend) # 这里是 RedisCache 对象了
      except ImportError as e:
          raise InvalidCacheBackendError(
              "Could not find backend '%s': %s" % (backend, e))
      return backend_cls(location, params) # 传入参数
  ```

  

## 2.3 import_string

- 这个接口是根据从settings文件之中读取的内容，然后进行对应模块的导入

  ```python
  # 根据路径导入模块，然后从中取出对应的RedisCache类
  def import_string(dotted_path):
      """
      Import a dotted module path and return the attribute/class designated by the
      last name in the path. Raise ImportError if the import failed.
      """
      try:
          module_path, class_name = dotted_path.rsplit('.', 1)  # module_path:'django_redis.cache' # class_name:'RedisCache'
      except ValueError as err:
          raise ImportError("%s doesn't look like a module path" % dotted_path) from err
  
      module = import_module(module_path)  # 获取django_redis.cache模块
  
      try:
          return getattr(module, class_name) # 从模块之中获取 RedisCache 属性，也就是获取RedisCache这个类
      except AttributeError as err:
          raise ImportError('Module "%s" does not define a "%s" attribute/class' % (
              module_path, class_name)
          ) from err
  
  ```

  

## 2.4 RedisCache

- 这个实际是，对redis接口的倒数第二层的一个封装

  ```python
  # Django RedisCache 包装器
  class RedisCache(BaseCache):
      def __init__(self, server, params): # 接收参数：server:"redis://127.0.0.1:6379/0" ,params:{'OPTIONS': {'CLIENT_CLASS': 'django_redis.client.DefaultClient'}}
          super(RedisCache, self).__init__(params) # 调用父类构造函数
  
          self._server = server
          self._params = params
  
          options = params.get("OPTIONS", {}) # {'CLIENT_CLASS': 'django_redis.client.DefaultClient'}
  				
          self._client_cls = options.get("CLIENT_CLASS", "django_redis.client.DefaultClient") # 获取路径 django_redis.client.DefaultClient
          self._client_cls = load_class(self._client_cls) # 从路径之中加载类：也就是加载了DefaultClient类(从django_redis.client 获取DefaultClient 这个类)
          self._client = None
          self._ignore_exceptions = options.get("IGNORE_EXCEPTIONS", DJANGO_REDIS_IGNORE_EXCEPTIONS)
  
      @property
      def client(self):
          """
          Lazy client connection property.
          """
          if self._client is None:
              self._client = self._client_cls(self._server, self._params, self) # 创建一个客户端 server:"redis://127.0.0.1:6379/0" params:{'OPTIONS': {'CLIENT_CLASS': 'django_redis.client.DefaultClient'}}
          return self._client
  
  ```

## 2.5 DefaultClient

- 对redis的第一层包装

  ```python
  # 默认redis客户端类
  class DefaultClient(object):
      def __init__(self, server, params, backend): # server:"redis://127.0.0.1:6379/0" params:{'OPTIONS': {'CLIENT_CLASS': 'django_redis.client.DefaultClient'}}
          self._backend = backend
          self._server = server
          self._params = params
  
          self.reverse_key = get_key_func(params.get("REVERSE_KEY_FUNCTION") or
                                          "django_redis.util.default_reverse_key")
  
          if not self._server:
              raise ImproperlyConfigured("Missing connections string") # 配置不正确
  			
  		def get_client(self, write=True, tried=(), show_index=False):
  			#  获取原始redis客户端的方法
  			# 几乎所有的缓存后端都使用这个函数 获取本地redis客户端/连接的操作 实例
  ```

  

# 三、缓存配置详解

[参考链接](https://blog.csdn.net/cuomer/article/details/80990246?ops_request_misc=&request_id=&biz_id=102&utm_term=Django%20%E7%BC%93%E5%AD%98%E8%AE%BE%E7%BD%AE%E4%B8%AD%20VERSION%E5%AD%97%E6%AE%B5%E7%9A%84%E4%BD%9C%E7%94%A8&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-1-80990246.142^v9^control,157^v4^control&spm=1018.2226.3001.4187)

- 配置设置

  ```python
  REDIS_LOCATION = "redis://127.0.0.1:6379/0"  #redis的ip、端口、和第几个库
  CACHES = {
      "default": {
          "BACKEND":
              "django_redis.cache.RedisCache",  # 后端redis引擎模块(django中redis缓存模块所在)
          "LOCATION": REDIS_LOCATION,  # 连接的服务器位置
          "OPTIONS": {
              "CLIENT_CLASS":
                  "django_redis.client.DefaultClient",  ##使用什么来连接redis数据库
          },
        	# 这里的VERSION和KEY_PREFIX 都只是构成key的一部分.这两个参数保存在父类之中
        	# eg:m:version:tb:sk(tb是后来添加的前缀)
        	# 也就是说在django之中只需要使用tb:sk 即可以进行数据的访问，前缀和版本会自动添加上
        	# 注意这种方式不适用于直接获取底层版本
  				"VERSION":"version",
  				"KEY_PREFIX":"m"
      }
  }
  ```

  

# 四、理解

## 4.1 使用缓存的方法

- 在使用redis缓存的时候，有两种方法
  - 直接通过cache对象进行缓存的使用(**django自身对缓存的包装**)
  - 深入源码之中，通过cache对象获取redis的原始接口对象

- 两种方法的区别
  - 第一种方法，由于是django自身进行了包装，因此是更加高级的接口，所以在读取settings文件时，能够利用到更多的参数
  - 第二种方法更加的接近底层，因此读取setting之中的文件配置更少
  - 如下图所示，这是第一种方法，因为多了一层包装，所以在set的时候会进行key的构造，**然后调用get_client**获取客户端，比直接获取客户端多了一层![image-20220507144307280](https://gitee.com/yao-cunhao/ssh_picture/raw/master/pict/202205071443572.png)