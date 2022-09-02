# 不使用Nginx无法访问静态资源

- 这是因为uWSGI是从`application`和Django框架进行连接的，因此是通过接口进行访问，而不是直接访问路径下的资源

- 所以需要配置一个`url`路径

  ```python
  # url 文件
  from django.contrib import admin
  from django.urls import path
  from django.conf import settings
  from django.conf.urls.static import static
  
  urlpatterns = [
      path('admin/', admin.site.urls),
  ] +  static(settings.STATIC_URL, document_root=settings.STATIC_ROOT)
  
  
  # STATIC 设置
  STATIC_URL = '/static/'
  STATIC_ROOT = os.path.join(BASE_DIR, "static")  # 每个app自己的static文件夹
  STATICFILES_DIRS = ( # 公有的文件夹
       os.path.expanduser('/home/didi/map_config/map_config/static/cc'),  # uWSGI是否能访问到资源和这个没什么关系
  )
  ```

- **注意**

  - 如果关闭DEBUG模式，将无法访问资源
  - [原因](https://blog.csdn.net/qq_36874480/article/details/100652364?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522166185747216782390540018%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fall.%2522%257D&request_id=166185747216782390540018&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~pc_rank_34-4-100652364-null-null.142^v42^pc_rank_34,185^v2^control&utm_term=Django%20%E5%85%B3%E9%97%AD%E8%B0%83%E8%AF%95%E6%A8%A1%E5%BC%8F%E5%90%8E%E6%97%A0%E6%B3%95%E8%AE%BF%E9%97%AE%E9%9D%99%E6%80%81%E8%B5%84%E6%BA%90&spm=1018.2226.3001.4187)