# 一、配置方法

- 从配置文件之中进行读取和运行的方法
- 配置文件之中的一些变量含义
- [文档链接](https://uwsgi-docs.readthedocs.io/en/latest/Configuration.html)

## 1.1 使用uwsgi.yaml 启动

- 启动方式：`uwsgi --y/yaml xxx.yaml`
- 重启方式：uwsgi  --reload uwsgi.pid
- 停止方式：uwsgi --stop uwsgi.pid

# 二、配置选项

- [文档链接](https://uwsgi-docs.readthedocs.io/en/latest/Options.html)

# 三、日志模块

- [参考链接](https://uwsgi-docs.readthedocs.io/en/latest/Logging.html)

- 开启日志之时，默认是向标准收入.... 进行输出，通过设置 daemonize 将信息重定向文件之中

- ——logger将为每条消息设置一个记录器

-  ——req-logger将为请求信息消息设置一个记录器

  ```nginx
      # log config 
      log-reopen     : true  # reopen log after reload
      logdate        : true  # 日志以时间戳开始
      disable-logging : true #  禁用日志记录请求(url 等等)
      log-master: true  # 主进程来启动日志
      daemonize:     %d/../logs/uwsgi.log (内容重定向文件)
      stats          : :8001 # 内部统计数据导出为JSON格式
  ```

  

# 四、使用uWSGI、nginx 配置Django服务

- 通常不直接使用uWSG接收服务器服务，因为nginx的服务器处理的性能更加的高效



# 五、uWSGI和Nginx

- [官网参考](https://uwsgi-docs-zh.readthedocs.io/zh_CN/latest/Nginx.html)