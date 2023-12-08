- [link](https://blog.csdn.net/weixin_42278281/article/details/119541072)

  - python manage.py runserver该运行方式启动项目，这种方式会启动两个进程，其中一个进程用于监听用户修改代码后重新启动，但在真实环境中使用uwsgi启动项目则只会调用一次。如果你不愿意启动两个进程，也可以使用 python manage.py runserver --noreload启动项目, --noreload关闭监听代码修改重启程序的意思。


​    