[登录页面](https://blog.csdn.net/m0_37422217/article/details/106965085?ops_request_misc=&request_id=&biz_id=102&utm_term=%E6%88%91%E4%BB%AC%E5%9C%A8%20BookStore%20%E9%A1%B9%E7%9B%AE%E7%9A%84%20user%20%E5%BA%94%E7%94%A8%E4%B8%AD%20mod&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-0-106965085.142^v10^pc_search_result_control_group,157^v8^control&spm=1018.2226.3001.4187)

[模板，视图函数等等](https://blog.csdn.net/weixin_30482383/article/details/99356889?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522165332141816780357249411%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fall.%2522%257D&request_id=165332141816780357249411&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~rank_v31_ecpm-3-99356889-null-null.142^v10^pc_search_result_control_group,157^v8^control&utm_term=%E6%88%91%E4%BB%AC%E5%9C%A8+BookStore+%E9%A1%B9%E7%9B%AE%E7%9A%84+user+%E5%BA%94%E7%94%A8%E4%B8%AD+models.py+%E7%9A%84+User+Model+%E4%B8%AD%E6%B7%BB%E5%8A%A0%E4%BB%A5%E4%B8%8B%E4%BB%A3%E7%A0%81%EF%BC%9A&spm=1018.2226.3001.4187)

```python
 # 规定访问静态文件的url中的格式
 # 当STATIC_URL='/static/'时，那么访问静态资源就是通过/static/t.css来访问静态资源；
 # 当STATIC_URL='/media/'时，那么访问静态资源就是通过/media/t.css来访问静态资源。
  # STATIC_URL的作用是用于拼接静态文件的存储路径
STATIC_URL = '/static/' # 规定访问静态文件的url中的格式,app私有目录

# STATIC_ROOT是收集静态文件时，将静态文件转移的目录，不能和STATICFILES_DIRS一致。
# 收集静态文件会将所有app下static目录中的文件和STATICFILES_DIRS中的文件收集到STATIC_ROOT目录中。
# 一般是上线才会使用，方便nginx直接访问
# 当DEBUG设置成False之后,下面的STATICFILES_DIRS下的静态文件将会失效，那么如何此时STATIC_ROOT将会发挥它重要的作用

STATIC_ROOT = os.path.join(BASE_DIR, "static")

# 静态文件可以放在每一个django app目录下的static文件夹中。也可以放在STATICFILES_DIRS指定的目录下。这个配置项不是必须的
# 在配置文件中配置STATICFILES_DIRS为静态文件的存储路径
STATICFILES_DIRS = (
    os.path.expanduser('~/fe/voyager/offboard/cloud/src/fe_static/output/'),
) # 公有静态资源目录

BASE_DIR:获取项目的目录位置


STATIC_HOST = os.getenv('STATIC_HOST', '10.190.9.216:3002') # Nginx 配置静态文件路径
IMG_BASE_URL = "http://%s/static/img" % STATIC_HOST
MAP_EDITOR_GIFT_URL = "http://%s/static/img/" % STATIC_HOST

```

