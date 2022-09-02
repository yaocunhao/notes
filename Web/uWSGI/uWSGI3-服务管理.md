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
  
  ```

  