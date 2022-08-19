# 一、介绍

- 在Django 之中使用mongo，和平常使用MySql 很多类似的地方，不过额外的提供了，使用python_mongo 的方法

- 在定义的类中的管理器对象继承DjongoManager 即可

- 源码分析

  ```python
  class DjongoManager(Manager):
      """
      This modified manager allows to issue Mongo functions by prefixing
      them with 'mongo_'.
  
      This module allows methods to be passed directly to pymongo.
      """
  		
      # 连接python mongo 服务器
      # 拆分mongo前缀
      def __getattr__(self, name):
          if name.startswith('mongo'):
              name = name[6:]
              return getattr(self._client, name)
          else:
              return super().__getattr__(name)
  
      @property
      def _client(self):
          return (
              pymongo_connections[self.db]
                  .cursor()
                  .db_conn[self.model._meta.db_table]
          )
    
    # 所以在程序之中的使用方式为 obj.mongo_find()
  ```

  