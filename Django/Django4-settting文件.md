- DATABASE_APPS_MAPPING
  - 设置app使用的数据库
- DATABASE_ROUTERS 
- DATABASE
  - 设置数据库信息
- CACHES
  - 设置缓存信息
- INSTALLED_APPS
  - app信息
- MIDDLEWARE
  - 中间件信息
- TEMPLATES
  - 模板信息
- STATIC_URL
  - 静态资源路径，**当前私有路径**
  - 它指的是静态资源的存放位置，静态资源包括 CSS、JS、Images。比如我们要在项目中添加一些图片，通常这些静态图片被存放在新建的 static 目录下，这样就实现了通过 STATIC_URL= '/static/' 路径对静态资源的访问
- STATICFLES_DIRS
  - 静态文件公有目录，如果私有目录找到了就不会来这里找(惰性查找)，如果没有找到就来这里找
- STATIC_HOST
  - 静态资源主机路径

- BASE_DIR

  - 当前项目所在的路径,通常绑定方法如下

    ```python
    # __file__是 Python 的语法，显示当前文件的位置，os.path.abspath(__file__) 方法返回当前文件的绝对路径c
    BASE_DIR=os.path.dirname(os.path.dirname(os.path.abspath(__file__)))
    ```

- DEBUG
  - 用于配置Django项目的启用模式
    - DEBUG = True用于在开发环境中使用，属于调试模式，在项目的运行过程中会暴露一些错误信息以方便调试。
    - DEBUG = False用于线上环境，表示不启用调试模式

- ALLOWED_HOSTS

  - 用于配置能够访问当前站点的域名（IP地址），当 DEBUG = False 时，必须填写，有以下三种使用方法：

    - []，空列表,表示只有1217.0.0.1，localhost能访问本项目
    - ['*']，表示任何网络地址都能访问到当前项目
    - ['192.168.1.3', '192.168.3.3'] 表示只有当前两个主机能访问当前项目

    - **提示**：如果是在局域网，让其它主机也能访问此站点，应使用 ALLOWED_HOSTS=['*'] 的方式