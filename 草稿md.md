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





```
333407.223731429 3468974.86659408 20.9949305905084 51R

333407.219034517 3468974.87542239 20.9949305905084 51R

333407.219034517 3468974.87542239 19.3524505905084 51R

333407.223731429 3468974.86659408 19.3524505905084 51R

```

```python
box 的创建
http://10.190.9.216:6005/map_editor/api/v2/box
{
  "x": 333406.8922464547,
  "y": 3468974.695898181,
  "z": 20.17369059050837,
  "length": 0.01,
  "width": 0.7456387098375671,
  "height": 1.6424836113490642,
  "roll": 0,
  "pitch": 0,
  "yaw": 5.201332259255591,
  "zone": "51R",
  "obj_id": 0,
  "obj_type": 3
}

signal 的创建
http://10.190.9.216:6005/map_editor/api/v2/signal
{
  "position_point": {
    "x": 333459.94854671234,
    "y": 3469911.84650886,
    "z": 20.015154866151427,
    "zone": "51R"
  },
  "condition": 1,
  "box_id": 37,
  "lights": [
    1
  ]
}

# 生成的四个点
333460.072906574 3469911.53938357
333460.082117856 3469911.54327617
333460.072906574 3469911.53938357
333460.082117856 3469911.54327617

333453.24 3469908.38


```



-  回顾上半年，我完成了哪些工作/任务，对组织/团队产生了什么价值/贡献？存在哪些不足？
  - 完成的工作
    - map_editor
      - 优化在 lane 前后继取消问题
      - **增加“栅栏”的zone的类型**
      - **在road上增加一键生成 hardboundary 的功能**
      - 元素缓存查看及管理工具
      - **添加新元素kerb_line**
      - 搭建一个内部稳定使用的环境，定时同步最新发版的历史数据
      - 优化同步脚本，减少同步花费的时间
    - onepiece
      - **搭建美东服务**
      - **定时导入数据**
      - **添加版本查询功能**
  - 贡献价值
    - 在日常工作之中，作为一颗螺丝钉，能够很及时的完成一些开发工作
  - 不足
    - 对项目的整体工作还不够熟悉，对一些细节的把握还是有所缺失，导致一些工作的效率不是那么高效

- 回顾上半年，我有哪些收获和成长?

  - 作为一名新人，在这半年之中收获颇为丰满。首先日常工作对个人的基础知识的运用和学习起到了非常大的校验和指引作用。其次

- 展望下半年，我期待获得哪些成长？并匹配哪些行动计划?

  - 在下半年中，应该更加熟悉业务流程和代码结构，努力降低一些工作中的低级错误。并且积极承担更多的工作内容，努力实现个人价值

    ```
    优化在 lane 前后继取消问题，增加“栅栏”的zone的类型，在road上增加一键生成 hardboundary 的功能，元素缓存查看及管理工具，添加新元素kerb_line，搭建一个内部稳定使用的环境，定时同步最新发版的历史数据，优化同步脚本，减少同步花费的时间
    ```

    

    

这是一把简单的麻将声音  就是目前的大键声音着实是优点拉胯的什么叫做什么麻将引这就是麻将

 现在空格和回车键换成了酒红轴，

什么他妈的叫做他妈的惊喜，原来这就叫做他妈的麻将银色啊     
