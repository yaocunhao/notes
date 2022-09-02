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

- 查找静态资源

  - [参考链接](https://blog.csdn.net/wangctes/article/details/89055771?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522165692435016780366592153%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=165692435016780366592153&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~baidu_landing_v2~default-1-89055771-null-null.142^v30^pc_rank_34,185^v2^control&utm_term=STATIC_URL+STATIC_ROOT+STATICFILES_DIRS&spm=1018.2226.3001.4187)
  - [参考链接2](https://blog.csdn.net/cuisidong1997/article/details/117836447?spm=1001.2101.3001.6650.1&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7Edefault-1-117836447-blog-102022093.pc_relevant_default&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7Edefault-1-117836447-blog-102022093.pc_relevant_default&utm_relevant_index=2)
  - STATIC_URL
    - 作用是通过url直接访问在项目中的静态文件,不需要像函数访问一样设置url
  - STATIC_ROOT
    - STATIC_ROOT 是在部署静态文件时，所有的静态文静聚合的目录
    - `python manage.py collectstatic`， [django](https://so.csdn.net/so/search?q=django&spm=1001.2101.3001.7020)会把所有的app下面的static文件都复制到STATIC_ROOT文件夹下
  - STATICFILES_DIRS
    - 公共资源文件夹
  - 资源的查找方式
    - 首先到STATICFILES_DIRS里面寻找静态文件,其次再到各个app的static文件夹里面找
    - django查找静态文件是惰性查找,查找到第一个,就停止查找了

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