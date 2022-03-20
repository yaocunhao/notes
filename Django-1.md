# 一、项目的创建

## 1.1创建一个名为BookStore的Django项目

- `django-admin startproject BookStore`![image-20220320160013211](https://raw.githubusercontent.com/yaocunhao/picture/main/image-20220320160013211.png)

- 配置文件详解
  - manage.py文件
    - 一级子目录中的 manage.py 文件是管理 Django 项目的重要命令行工具，它主要用于启动项目、创建应用和完成数据库的迁移等
  - __init__.py文件
    - 二级子目录中的 __init__.py 文件用于标识当前所在的目录是一个 Python 包，如果在此文件中，通过 import 导入其他方法或者包会被 Django 自动识别
  - settings.py文件
    - settings.py 文件是 Django 项目的重要**配置文件**。项目启动时，settings.py 配置文件**会被自动调用**，而它定义的一些全局为 Django 运行提供参数，在此配置文件中也可以自定义一些变量，用于全局作用域的数据传递
  - urls.py文件
    - url.py 文件用于**记录 Django 项目的 URL 映射关系**，它属于项目的基础路由配置文件，**路由系统就是在这个文件中完成相应配置**的，项目中的动态路径必须先经过该文件匹配，才能实现 Web 站点上资源的访问功能
  - wsgi.py 是 WSGI（Web Server Gateway Interface）服务器程序的入口文件，主要用于启动应用程序。它遵守 WSGI 协议并负责网络通讯部分的实现，只有在项目部署的时候才会用到它
- `python manage.py startapp index` 
  - 创建一个名为index的App