# voyager环境搭建

## 一、文档

- 代码仓库![image-20220225120654152](/Users/didi/Library/Application Support/typora-user-images/image-20220225120654152.png)
- 环境配置、整体认知
  - voyager环境是无人车整体代码跑的环境，和车上真实环境一样。其实我们的代码开发可以在centos下进行
  - voyager环境的配置需要在台式机上进行配置
  - 我们的mac电脑可以在centos上进行python环境的配置
- 第一次谈话总结
  - 方向走错、埋头苦干、闭门造车
  - 不懂要即使问

## 二、当前业务流程

- 数据采集(无人车雷达扫描)
- 数据融合
- 数据的标注(当前的工作)
- 数据的打包
- 传输数据到保密室，再由"地图"部门刻录光盘，交付无人车





## 三、Run_Server 记录

- 启动过程

  - pipenv shell、./constall install
  - 如果发生确实，直接pip install,然后退出虚拟环境，再次进入即可

- 环境变量发生变化 

  - export DJANGO_SETTINGS_MODULE=trail.settings_test #代码中已经变更
  - export REDIS_HOST=10.96.93.235   #需要设置
  - export DATABASE_NAME=xxz_map_editor # 需要设置，在.ven中进行设置

- 启动python虚拟环境 

  - 启动方法
    - 方法1：source .venv/bin/activate  退出 deactivate
    - 方法2： pipenv shell  退出 exit()

- 无图片显示原因

  - 导入fe文件

  - /home/ych/voyager_w/map_server/trail/settings_test.py之中进行修改

    ```python
    #IMG_BASE_URL = "http://%s/static/img" % STATIC_HOST
    IMG_BASE_URL = "http://%s/static/image" % STATIC_HOST
    ```

  - /home/ych/voyager_w/map_server/map_editor/services/regions之中进行修改

    ```python
    #TILES_URL = "%s/tiles/%%s/{z}/{x}/{y}.png?version=%%s" % settings.IMG_BASE_URL
    TILES_URL = "%s/%%s/{z}/{x}/{y}.png?version=%%s" % settings.IMG_BASE_URL
    ```

- 无法安装mysqlclient问题

  - 没有安装mysql -> 进行安装即可

  - 问题二解决：`LDFLAGS=-L/usr/local/opt/openssl/lib pip3 install mysqlclient Collecting mysqlclient`

  - ```
    export LDFLAGS="-L/usr/local/opt/openssl/lib"
    export CPPFLAGS="-I/usr/local/opt/openssl/include"
    ```

- 普通用户无法启动虚拟环境问题

  - 权限不够 -> 将对应的文件权限进行更改即可，注意用`""`将有空格的文件夹名称包起来即可以识别带空格的文件夹了

- pipenv install --python3.6 设置虚拟环境的python版本，python版本直接去官网下载即可

- 启动

  - python3 manage.py runserver 0.0.0.0:6001



# 四、操作

- 清缓存
  - map_editor.scripts.delete_caches.py
- 

