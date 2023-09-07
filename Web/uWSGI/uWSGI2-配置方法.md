# 一、配置方法

- 从配置文件之中进行读取和运行的方法
- 配置文件之中的一些变量含义
- [文档链接](https://uwsgi-docs.readthedocs.io/en/latest/Configuration.html)
- skip-atexit-teardown
  - 您可以使用uwsgi的`skip-atexit-teardown`选项来跳过在退出时执行的清理操作。此选项可以在uwsgi配置文件或命令行中设置。在uwsgi配置文件中，您可以将`skip-atexit-teardown`设置为1来禁用退出时的清理操作.使用这个选项，uwsgi将在退出时跳过清理操作，这可能会提高退出速度。请注意，跳过清理操作可能会导致一些资源无法正确释放，因此请谨慎使用

- uwsig lazy-apps
  - uWSGI是一个Web服务器和应用服务器，它支持多种编程语言和框架。"lazy-apps"是uWSGI配置选项之一，它允许延迟加载应用程序。
  - 在uWSGI中，应用程序可以被设置成立即加载（immediate-apps）或者延迟加载（lazy-apps）。当应用程序被立即加载时，uWSGI会在启动时预加载所有应用程序。而当应用程序被延迟加载时，uWSGI只会在第一个请求到达时才加载相应的应用程序。
  - 使用"lazy-apps"选项可以提高服务器的启动速度，特别是在有多个应用程序的情况下。因为只有在需要时才会加载应用程序，可以减少启动时的资源消耗

- vacuum
  - 它用于在请求处理后自动回收资源
  - 当启用vacuum选项时，uWSGI会在每个请求处理完成后自动清理应用程序和服务器中的临时资源，例如数据库连接、临时文件等。这可以帮助减少内存使用和资源泄漏，并提高服务器的性能和稳定性


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