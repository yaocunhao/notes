# 一、数据库操作

- 数据库迁移

  - python manage.py migrate
  - “迁移”就是把 Django 默认自带应用的数据表迁移到我们自己的创建的数据库里，有点像搬家的意思，migrate 这个单词的含义就是“迁移”![image-20220315112642428](../../Library/Application Support/typora-user-images/image-20220315112642428.png)

- migrate与makemigrations命令详解

  - makemigrations 命令会检测应用目录下是否存在 migrations 目录，如果没有则进行创建。首先，会根据应用的表结构定义生成一个 0001_inital.py 文件，里面定义了数据表的结构

  - 当数据表更改后，我们首先执行 makemigrations 命令，然后 Django 会重新生成一个新的数据库迁移文件用来记录表结构之间的差异，命名规则是对上一个迁移文件的序列号加1，如 0002_xxx、0003_xxx

  - 之后，再次执行 migrate 命令让新的迁移文件生效并同步回数据库，从而完成表结构定义的修改。对于 Django 内置的应用，数据库迁移文件已经生成好了，所以直接使用 migrate 命令即可

  - 每一次数据表更改后，都需要执行下面的两个命令，它们的执行顺序如下所示：

    ```python
    python manage.py makemigrations # 生成一个数据库迁移文件:用来记录表结构之间的差异
    python manag.py migrate #让迁移文件生效 && 同步回数据库：完成表结构定义的修改
    ```

# 二、创建应用

- 什么是应用(APP)
  - Django 项目就是基于 Django 框架开发的 Web 应用，它包含了一组配置和多个应用，我们把应用称之为 App
  - 比如 auth、admin，它们都属于 APP
  - 一个 App 就是一个 Python 包，通常一个 App 可以包含模型、视图、模板和 URL 配置文件，可以被应用到多个 Django 项目中，因为它们的本质就是可被重用的 Python 软件包

- 创建应用的过程是非常简单的，利用 manage.py 提供的 startapp 命令就可以创建一个APP，具体命令如下所示：

  ```
  python manage.py startapp index
  ```

  - startapp 同样也属于 manage.py 的子命令，用来创建 Django 的应用。执行这个命令不会在 CMD 命令行看到任何输出，但是，可以在 manage.py 的同级目录下看到多出了一个 index 目录。**目录下各文件作用如下**
    - admin.py 用于将 Model 定义的数据表注册到管理后台，是 Django Admin 应用的配置文件；
    - apps.py 用于应用程序本身的属性配置文件；
    - models.py 用于定义应用中所需要的数据表；
    - tests.py 文件用于编写当前应用程序的单元测试；
    - views.py 用来定义视图处理函数的文件；
    - 一级目录下的 `__init__.py` 文件标识 index 应用是一个 Python 包；
    - migrations 目录用于存储数据库迁移时生成的文件，该目录下的` __init__.py `文件标识 migrations 是一个 Python 包。

- 这就是 index 应用涉及到的所有文件，当然在实际的开发工作中，该应用目录下的文件也不是一成不变的，开发者根据自己的需要会相应的增加文件或者子目录，比如 urls.py 文件或者存储静态文件的 static 目录等。所以大家千万不要认为 Django 框架自动生成的目录，无需我们做其他操作或者更改，这是使用 Django 的一个误区

- **创建完后，记得添加应用**

  ```python
  # 当前项目中用来安装的应用（APP）的列表
  INSTALLED_APPS = [
      'django.contrib.admin',
      'django.contrib.auth',
      'django.contrib.contenttypes',
      'django.contrib.sessions',
      'django.contrib.messages',
      'django.contrib.staticfiles',
      'index',
  ]
  ```

# 三、OMR

## 3.1 OMR介绍

- 是什么
  - 对象关系映射，它是一种基于关系型数据库的程序技术。ORM 允许你使用类和对象对数据库进行操作，这大大提高了对数据库的控制，避免了直接使用 SQL 语句对数据库进行操作。这种程序技术的底层主要是通过映射机制实现的

- Web 开发中对数据库的操作是必不可少的，然而每种数据库的操作方式以及用法不尽相同。由于 Django 中 ORM 的存在，为我们操作不同种类的数据库提供了统一的方法，ORM 适配了多种常用的关系型数据库，例如 PostgreSQL、MySQL、Oracle、Sqlite3 等![image-20220315113408480](../../Library/Application Support/typora-user-images/image-20220315113408480.png)

  - 如图1是 ORM 与数据库的映射关系图。ORM 把类映射成数据库中的表，把类的一个实例对象映射成数据库中的数据行，把类的属性映射成表中的字段，通过对象的操作对应到数据库表的操作，实现了对象到 SQL、SQL 到对象转换过程

- Django 把表模型定义为 Model，他需要继承自`django.db.models`中的 Model 类，只要是与数据表相关的操作，都需要继承这个类。同时ORM 对于数据库的的增删改查，也提供了一些简单的 API，例如 F 查询、Q 查询

- 针对数据库中的字段类型，Django ORM 都有对应的 “xxxField” 来表述，见如下表格

- | 字段          | 说明                                                         | 字段属性                                                     |
  | ------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
  | AutoFiled     | 默然自增主键（Primary_key=Ture），Django 默认建立id字段为主键。 |                                                              |
  | CharFiled     | 字符类型                                                     | Max_length=32，字符长度需要明确                              |
  | IntgerFiled   | 整型 int                                                     |                                                              |
  | DateFiled     | 年月日时间类型                                               | auto_now=True，数据被更新就会更新时间 ；auto_now_add=True，数据第一次参数时产生。 |
  | DateTimeFiled | 年月日小时分钟秒时间类型                                     | auto_now=True，数据被更新就会更新时间； auto_now_add=True，数据第一次参数时产生。 |
  | DecimalFiled  | 混合精度的小数类型                                           | max_digits=3，限定数字的最大位数(包含小数位)；decimal_places=2，限制小数的最大位数。 |
  | BooleanFiled  | 布尔字段，对应数据库 tinyint 类型数据长度只有1位。           | 值为True或False                                              |
  | TextFiled     | 用于大文本                                                   |                                                              |

## 3.2 Django之中定义数据表

- 模型类
  - 其实模型类本质上属于一个 Python 类，只不过在 Django 中称之为做模型类 ，它是由 django.db.models.Model 派生出的子类，在 Django 中模型类是数据交互的接口，**一个模型类代表数据库中的一张数据表，模型类中每一个类属性都代表数据表中的一个字段，每一个对象代表一行数据**
  - 我们可以这样理解：Django 中模型类就相当于 ORM 模块

- 定义数据表

  - 现在有一张用户信息表 UserInfo，它有两个字段 name 和 password，可以定义如下：

    ```python
    from django.db import models # 导入包
    class UserInfo（models.Model）: #进行继承，类名=表名
          name = models.CharFiled(max_length=100) #属性 = 字段，
          password = models.CharFiled(max_length=100)
    ```

  - 通过以上代码，UserInfo 数据表就已经创建完成，我们对代码进行逐行解析：

    - 第 1 行，使用 from django.db import models 导入 models 模块；

    - 第 2 行，使用 class 关键字对 UserInfo 表进行类定义，并继承了models 模块中的 Model 类；

    - 第3、4 行，数据表中的字段 name 和 password 是 UserInfo 类的属性，name 和 password 字段类型都是 CharFiled，字段长度均是100

## 3.3 ORM 管理器对象

- 那么应该怎样对数据表进行操作呢？

  - 下面是插入数据的一种方法：**直接使用类名（即数据表名）来插入数据**

    ```python
    UserInfo.objects.create(name='jay',password='abc123') # 进行数据的插入
    ```

    - **每个继承自 `models.Model` 的模型类，都会有一个 objects 对象被同时继承下来，这个对象就叫做“管理器对象”，数据库的增删改查可以用 objects 管理器对象来实现**

  - 第二种方法：**创建 UserInfo 的实例对象，然后调用`save()`方法保存**

    ```python
    Obj=UserInfo（name="jay",password="abc123"）
    Obj.name="john"
    Obj.save()
    ```

- ORM 的增删改查称为 CURD 操作，下面列举几个常用语句

  ```python
  UserInfo.objects.all()#查询表中的所有记录
  UserInfo.objects.filter(name_contains='j')#查询表中name含有“j”的所有记录,被使用较多
  UserInfo.objects.get(name="john")#有且只有一个查询结果，如果超出一个或者没有,则抛出异常
  UserInfo.objects.get(name="john").delete()#删除名字为john的记录
  UserInfo.objects.get(name="john").update(name='TOM')#更新数据表的name为TOM
  ```

  