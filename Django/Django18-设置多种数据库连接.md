- 设置setting的数据库配置

  ```python
  DATABASES = {
      'default': {
          'ENGINE': 'django.db.backends.mysql',
          'NAME': DATABASE_NAME,
          'HOST': DATABASE_HOST,
          'USER': DATABASE_USER,
          'PASSWORD': DATABASE_PASSWD,
          'PORT': DATABASE_PORT
      },
      'test_mongodb':{
          'ENGINE': 'djongo',
          'NAME': MONGODB_NAME,
          'HOST': f'mongodb://{MONGODB_HOST}:{MONGODB_PORT}/',
          'USER': MONGODB_USER,
          'PASSWORD': MONGODB_PASSWD,
          'ENFORCE_SCHEMA': False,
      }
  }
  ```

- 设置app使用的数据库

  ```python
  DATABASE_APPS_MAPPING = {
    'mongo_test':'test_mongodb'  # 这里的含义：mongo_test app 使用的数据库是 test_mongodb
  }
  ```

- 设置路由方式

  - [gd](https://docs.djangoproject.com/en/2.0/topics/db/multi-db/)
  - trail 可以是任意一个app模块，可以在其中找到对应的文件就行
  
  ```python
  DATABASE_ROUTERS = ['trail.database_router.DatabaseAppsRouter']
  ```
  
  - 在对应的模块下创建文件`database_router.py`，拷贝下面的路由代码
  
    ```python
    from django.conf import settings
    DATABASE_MAPPING = settings.DATABASE_APPS_MAPPING
    class DatabaseAppsRouter(object):
      """
      A router to control all database operations on models for different
      databases.
      In case an app is not set in settings.DATABASE_APPS_MAPPING, the router
      will fallback to the `default` database.
      Settings example:
      DATABASE_APPS_MAPPING = {'app1': 'db1', 'app2': 'db2'}
      """
      
      def db_for_read(self, model, **hints):
        """"Point all read operations to the specific database."""
        if model._meta.app_label in DATABASE_MAPPING:
          return DATABASE_MAPPING[model._meta.app_label]
        return None
      
      
      def db_for_write(self, model, **hints):
        """Point all write operations to the specific database."""
        if model._meta.app_label in DATABASE_MAPPING:
          return DATABASE_MAPPING[model._meta.app_label]
        return None
      
      
      def allow_relation(self, obj1, obj2, **hints):
        """Allow any relation between apps that use the same database."""
        db_obj1 = DATABASE_MAPPING.get(obj1._meta.app_label)
        db_obj2 = DATABASE_MAPPING.get(obj2._meta.app_label)
        if db_obj1 and db_obj2:
          if db_obj1 == db_obj2:
            return True
          else:
            return False
        return None
      
      
      
      def allow_syncdb(self, db, model):
        """Make sure that apps only appear in the related database."""
        if db in DATABASE_MAPPING.values():
          return DATABASE_MAPPING.get(model._meta.app_label) == db
        elif model._meta.app_label in DATABASE_MAPPING:
          return False
        return None
      
      
      
      def allow_migrate(self, db, app_label, model=None, **hints):
        """
        Make sure the auth app only appears in the 'auth_db'
        database.
        """
        if db in DATABASE_MAPPING.values():
          return DATABASE_MAPPING.get(app_label) == db
        elif app_label in DATABASE_MAPPING:
          return False
        return None
    ```
  
    
  