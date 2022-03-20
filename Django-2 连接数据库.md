# 一、数据库操作

- 数据库配置

  - 在项目的settings.py文件之中进行配置

    ```python
    #数据库配置
    DATABASES = {
        'default': {
            'ENGINE': 'django.db.backends.mysql', #使用什么数据库
            'NAME': 'bookstoredb', #数据库名字
            'USER': 'root', #用户
            'PASSWORD': '8888', #密码
            'HOST': '127.0.0.1', #主机号
            'PORT': '3306', #端口
        }
    }
    ```

  - **在项目允许前，登录数据库手动创建对应的数据库名称**

- 数据库迁移

  - `python manage.py migrate`
  - “迁移”就是把 Django 默认自带应用的数据表迁移到我们自己的创建的数据库里，有点像搬家的意思，migrate 这个单词的含义就是“迁移”
  - migrate 是 manage.py 的子命令，它主要用来将应用的数据表同步到数据库中。执行后，它会检查 I**NSTALLED_APPS 里配置**的应用列表，**依次为每个应用创建所需要的数据表**
  - 可以根据需要设置，如果项目本身用不到，进行注释即可![image-20220315112642428](https://raw.githubusercontent.com/yaocunhao/picture/main/image-20220315112642428.png)

- makemigrations命令

  - makemigrations 命令会检测应用目录下是否存在 migrations 目录，如果没有则进行创建。首先，会根据应用的表结构定义生成一个 0001_inital.py 文件，**里面定义了数据表的结构**

  - 当数据表更改后，我们首先执行 makemigrations 命令，然后 Django 会重新生成一个新的数据库迁移文件用来**记录表结构之间的差异**，命名规则是对上一个迁移文件的序列号加1，如 0002_xxx、0003_xxx

  - 之后，再次执行 **migrate 命令让新的迁移文件生效并同步回数据库，从而完成表结构定义的修改**。对于 Django 内置的应用，数据库迁移文件已经生成好了，所以直接使用 migrate 命令即可

  - 每一次数据表更改后，都需要执行下面的两个命令，它们的执行顺序如下所示：

    ```python
    python manage.py makemigrations # 生成一个数据库迁移文件:用来记录表结构之间的差异
    python manage.py migrate #让迁移文件生效 && 同步回数据库：完成表结构定义的修改
    ```

# 二、OMR

## 2.1 OMR介绍

- 是什么
  
  - 对象关系映射，它是一种基于关系型数据库的程序技术。ORM 允许你使用类和对象对数据库进行操作，这大大提高了对数据库的控制，避免了直接使用 SQL 语句对数据库进行操作。这种程序技术的底层主要是通过映射机制实现的
  
- 作用

  - Web 开发中对数据库的操作是必不可少的，然而每种数据库的操作方式以及用法不尽相同。由于 Django 中 ORM 的存在，为我们操作不同种类的数据库提供了统一的方法，ORM 适配了多种常用的关系型数据库，例如 PostgreSQL、MySQL、Oracle等等![image-20220315113408480](https://raw.githubusercontent.com/yaocunhao/picture/main/image-20220315113408480.png)
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

## 2.2 Django之中定义数据表

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

## 2.3 ORM 管理器对象

- 那么应该怎样对数据表进行操作呢？

  - 下面是**插入数据**的一种方法：**直接使用类名（即数据表名）来插入数据**

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


## 2.4 实际演练

### 2.4.1 在pyhton定义数据表(类)

- 在index.models之中定义数据表。**models对应的就是indexApp连接数据库的桥梁所在**

- 由于每一个数据表对应一个 Model 定义，每一个 Model 都是一个 Python 类，所以，Model 之间是可以继承的。Django 规定，所有的 Model 都必须继承自:`django.db.models.Model`

  ```python
  from django.db import models
  # settings之中设置了所用的数据库，定义的类就是定义了数据表，之后这些表就会在数据库之中
  class Book(models.Model):  # 创建 book 表
      title = models.CharField(max_length=30, unique=True, verbose_name='书名')
      public = models.CharField(max_length=50, verbose_name='出版社')
      price = models.DecimalField(max_digits=7, decimal_places=2, verbose_name='定价')
      def default_price(self):
          return '￥30'
      retail_price = models.DecimalField(max_digits=7, decimal_places=2, verbose_name='零售价', default=default_price)
      def __str__(self):
          return "title:%s pub:%s price:%s" % (self.title, self.public, self.price)
  
  class Author(models.Model):  # 创建作者表
      name = models.CharField(max_length=30, verbose_name='姓名')
      email = models.EmailField(verbose_name='邮箱')
      def __str__(self):
          return '作者：%s' % (self.name)
  
  
  class UserInfo(models.Model):  # 创建用户信息表
      username = models.CharField(max_length=24, verbose_name='用户注册')
      password = models.CharField(max_length=24, verbose_name='密码')
  
  ```

### 2.4.2 数据库迁移

- 上面的只是写在代码之中，想要体现在数据库之中还需要进行迁移

  - `python manage.py makemigrations` 生成迁移表，迁移表包含了创建数据表用到的信息，**这只是一个过渡的临时文件**，使用`python manage.py 迁移表名称`**查看对应的数据库应该要完成什么操作**

    ```python
    didi@DIDI-FVFDHCREP3XY BookStore % python manage.py sqlmigrate index 0001_initial
    BEGIN;
    -- Create model Author #创建Author表
    CREATE TABLE `index_author` (`id` integer AUTO_INCREMENT NOT NULL PRIMARY KEY, `name` varchar(30) NOT NULL, `email` varchar(254) NOT NULL); #SQL将要执行命令
    --
    .....
    ```

  - `python manage.py migrate` 完成迁移，也就是在数据库之中执行上述的命令，即实现了将**index 应用的模型类（models）转换成数据库中的数据表**![image-20220320192927598](https://raw.githubusercontent.com/yaocunhao/picture/main/image-20220320192927598.png)

- `__str__ `

  - 为 print 这样的打印函数设计的，它属于 python 的 object 基类的一个方法，也就是说 python 所有的类都有该方法

  - 如果没有这个方法，打印对象会显示对象的地址，如果定义了，则更具定义显示

    ```python
    class TBase:
        def __init__(self, _name='tom', _age='22'):
            self.name = _name
            self.age = _age
    
        def __str__(self):
            return self.name
    
    # 定义前
    # t = TBase()
    # print(t) <__main__.TBase object at 0x7fec65b17e48>
    
    # 定义后
    # t = TBase()
    # print(t) tom
    
    ```

    

