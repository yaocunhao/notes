# 一、创建一个名为BookStore的Django项目

- `django-admin startproject BookStore`
  - 需要注意的是 第一个BookStore是命令创建出来的，也就是所谓的项目根目录
  - 而生成的这些配置所在目录的名称是可以进行更改的![image-20220320160013211](https://gitee.com/yao-cunhao/ssh_picture/raw/master/pict/image-20220320160013211.png)

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

# 二、配置Pycharm

![截屏2022-03-20 下午7.51.47](https://gitee.com/yao-cunhao/ssh_picture/raw/master/pict/%E6%88%AA%E5%B1%8F2022-03-20%20%E4%B8%8B%E5%8D%887.51.47.png)

# 三、创建应用

- 什么是应用(APP)

  - Django 项目就是基于 Django 框架开发的 Web 应用，它包含了一组配置和多个应用，我们把应用称之为 App
  - 比如 auth、admin，它们都属于 APP
  - 一个 App 就是一个 Python 包，通常一个 App 可以包含模型、视图、模板和 URL 配置文件，可以被应用到多个 Django 项目中，因为它们的本质就是可被重用的 Python 软件包

- 创建命令`python manage.py startapp index`![image-20220320185135871](../../Library/Application Support/typora-user-images/image-20220320185135871.png)

- startapp 同样也属于 manage.py 的子命令，用来创建 Django 的应用。执行这个命令不会在 CMD 命令行看到任何输出，但是，可以在 manage.py 的同级目录下看到多出了一个 index 目录。**目录下各文件作用如下**

  - admin.py 用于将 Model 定义的数据表注册到管理后台，是 Django Admin 应用的配置文件
  - apps.py 用于应用程序本身的属性配置文件
  - models.py 用于定义应用中所需要的数据表；
  - tests.py 文件用于编写当前应用程序的单元测试
  - views.py 用来定义视图处理函数的文件
  - 一级目录下的 `__init__.py` 文件标识 index 应用是一个 Python 包；
  - migrations 目录用于存储数据库迁移时生成的文件，该目录下的` __init__.py `文件标识 migrations 是一个 Python 包。

- 这就是 index 应用涉及到的所有文件，当然在实际的开发工作中，该应用目录下的文件也不是一成不变的，开发者根据自己的需要会相应的增加文件或者子目录，比如 urls.py 文件或者存储静态文件的 static 目录等。所以大家千万不要认为 Django 框架自动生成的目录，无需我们做其他操作或者更改，这是使用 Django 的一个误区

- **创建完后，记得添加应用**

  ```python
  INSTALLED_APPS = [
      'django.contrib.admin',
      'django.contrib.auth',
      'django.contrib.contenttypes',
      'django.contrib.sessions',
      'django.contrib.messages',
      'django.contrib.staticfiles',
      'index', # 新添加的
  ]
  ```

  



