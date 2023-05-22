- [gd](https://docs.djangoproject.com/en/2.0/ref/models/)

#  一、数据库配置

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

  - **在项目启动前，登录数据库手动创建对应的数据库名称**

- 数据库迁移

  - `python manage.py migrate`
  - “迁移”就是把 Django 默认自带应用的数据表迁移到我们自己的创建的数据库里，有点像搬家的意思，migrate 这个单词的含义就是“迁移”
  - migrate 是 manage.py 的子命令，它主要用来将应用的数据表同步到数据库中。执行后，它会检查 I**NSTALLED_APPS 里配置**的应用列表，**依次为每个应用创建所需要的数据表**
  - 可以根据需要设置，如果项目本身用不到，进行注释即可![image-20220619230232787](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202206192302887.png)

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

  - Web 开发中对数据库的操作是必不可少的，然而每种数据库的操作方式以及用法不尽相同。由于 Django 中 ORM 的存在，为我们操作不同种类的数据库提供了统一的方法，ORM 适配了多种常用的关系型数据库，例如 PostgreSQL、MySQL、Oracle等等![image-20220619231316364](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202206192313497.png)
  - 如图1是 ORM 与数据库的映射关系图。ORM 把类映射成数据库中的表，把类的一个实例对象映射成数据库中的数据行，把类的属性映射成表中的字段，通过对象的操作对应到数据库表的操作，实现了对象到 SQL、SQL 到对象转换过程
- Django 把表模型定义为 Model，他需要继承自`django.db.models`中的 Model 类，只要是与数据表相关的操作，都需要继承这个类。同时ORM 对于数据库的的增删改查，也提供了一些简单的 API，例如 F 查询、Q 查询
- 针对数据库中的字段类型，Django ORM 都有对应的 “xxxField” 来表述

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

  - `python manage.py migrate` 完成迁移，也就是在数据库之中执行上述的命令，即实现了将**index 应用的模型类（models）转换成数据库中的数据表**![image-20220619231341005](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202206192313135.png)

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


# 三、SQL和Django映射介绍

## 3.1 数据库表名

- 模型类如果没有指明表名，Django默认以小写app应用名_小写模型类名为数据库名称
- 可以通过bd_table 指明数据库表名

## 3.2 字段

- [gd](https://docs.djangoproject.com/en/2.0/ref/models/fields/)
  - 字段分为
    - 字段的类型
      - 普通字段
      - 文件字段
    - 字段的约束(属性)

### 3.2.1 字段类型

| 字段            | 说明                                                        | 约束选项                                                     |
| --------------- | ----------------------------------------------------------- | ------------------------------------------------------------ |
| AutoFiled       | 自增主键（Primary_key=Ture），Django 默认建立id字段为主键。 |                                                              |
| BigAutoField    | 64位自增主键                                                |                                                              |
| CharFiled       | 字符类型                                                    | Max_length=32，字符长度需要明确                              |
| IntgerFiled     | 32位整型 int                                                |                                                              |
| BigIntegerField | 64位 长整形                                                 |                                                              |
| BooleanFiled    | 布尔字段，对应数据库 tinyint 类型数据长度只有1位。          | 值为True或False                                              |
| NullBooleanFied | 支持Null、True或False三种值                                 |                                                              |
| TimeFied        | 时间，同DateFiled                                           |                                                              |
| DateFiled       | 年月日时间类型                                              | auto_now=True，数据被更新就会更新时间 ；auto_now_add=True，数据第一次参数时产生。 |
| DateTimeFiled   | 年月日小时分钟秒时间类型                                    | auto_now=True，数据被更新就会更新时间； auto_now_add=True，数据第一次参数时产生。 |
| DecimalFiled    | 混合精度的小数类型                                          | max_digits=3，限定数字的最大位数(包含小数位)；decimal_places=2，限制小数的最大位数。 |
| TextFiled       | 用于大文本                                                  |                                                              |
| FileField       | 上传文件字段                                                |                                                              |
| imageField      | 继承FileField，对上传内容进行校验，确保是有效图片           |                                                              |
| BinaryField     | 二进制数据存储(尽量不要使用)                                |                                                              |

### 3.2.2 字段约束

| 选项         | 说明                                                         |
| ------------ | ------------------------------------------------------------ |
| null         | 如果为Ture，允许为空，默认为False。日期型、时间型以及数字型字段不接受空字符串。所以当设置 IntegerField，DateTimeField 型字段可以为空时，需要将 blank 与 null 均设为 True 才可以。 |
| blank        | 如果为True，允许为空，默认为False。如果是字符型字段 CharField 和 TextField，它们是用空字符串来存储空值的 |
| db_column    | 字段的名称，如果未指定，则使用属性的名称                     |
| db_index     | 是否创建索引，默认False                                      |
| default      | 默认值                                                       |
| primary_key  | 主键，默认False                                              |
| unique       | 唯一键，默认False                                            |
| choices      | 用于给字段设置选择的值，是一个可迭代的对象(list/tupple)，其实就是SQL之中的set/enum |
| verbose_name | 在admin后台管理系统上显示的名称，如果没有设置这个字段，Django将会直接展示字段名并且将字段中的下划线转变为空格 |
| JSONField    |                                                              |

```python
# 创建表
class UserInfo(models.Model):
    # 定义chocies参数的对应关系，以元组（或者列表）的形式进行表述：
    choices = (
        (male, '男性'),
        (female, '女性'),
    )
    gender = models.CharField(max_length=2,choices = choices,default='male')
```



## 3.3 主键

- django会为表创建自动增长的主键列，每个模型只能有一个主键列，如果使用选项设置某属性为主键列后django不会再创建自动增长的主键列
- 默认创建的主键列属性为id，可以使用pk代替，pk全拼为primary key

## 3.4 属性名语法

- 不能是python的保留关键字

- 不允许使用连续的下划线

- 定义语法

  ```python
  属性名= models.字段类型
  
  class Demo(models.Model):
    id = models.BigAutoField(primary_key=True)
    create_time = models.DateTimeField(auto_now_add=True)  # 创建记录之时会添加时间
    update_time = models.DateTimeField(auto_now_add=True)  # 更新记录之时，会改变时间
    demo_field = models.CharField(max_length=20, default='', db_index=True)
    str_field = models.CharField(max_length=20, default='', db_index=True)
  ```

## 3.5 外键

### 3.5.1 原理

- 在底层，django为当前的表添加了一个字段(`属性名_id`)，这个字段是一个外键，当访问的时候就会先找到这个外键，然后找到外键表之中的数据

### 3.5.2 外键删除时对应的操作

- 在设置外键的时候，需要通过**on_delete** 选项指明主表删除数据时，对外键引用数据如何处理，在django.db.models中包含了可选常量：

  - CASCADE 级联操作。如果外键对应的那条数据被删除了，那么这条数据也会被删除。

  - PROTECT 受保护。即只要这条数据引用了外键的那条数据，那么就不能删除外键的那条数据。

  - SET_NULL 设置为空。如果外键的那条数据被删除了，那么在本条数据上就将这个字段设置为空。如果设置这个选项，前提是要指定这个字段可以为空

  - SET_DEFAULT 设置为默认值，仅在该字段设置了默认值时可用

  - SET() 如果外键的那条数据被删除了。那么将会获取`SET`函数中的值来作为这个外键的值。`SET`函数可以接收一个可以调用的对象（比如函数或者方法），如果是可以调用的对象，那么会将这个对象调用后的结果作为值返回回去

  - DO_NOTHING 不采取任何行为。一切全看数据库级别的约束。

    ```python
    from django.conf import settings
    from django.contrib.auth import get_user_model
    from django.db import models
    
    # SET接收可调用对象
    def get_sentinel_user():
        return get_user_model().objects.get_or_create(username='deleted')[0]
    
    class MyModel(models.Model):
        user = models.ForeignKey(
            settings.AUTH_USER_MODEL,
            on_delete=models.SET(get_sentinel_user),
        )
    ```

### 3.5.3 表关系

#### 3.5.3.1 一对多(多对一)

- 应用场景：比如文章和作者之间的关系。一个文章只能由一个作者编写，但是一个作者可以写多篇文章。文章和作者之间的关系就是典型的多对一的关系

- 实现方式：一对多或者多对一，都是通过`ForeignKey`来实现的(**在Book表中，外键authro_id可以存在多个**)

  ```python
  # 类定义
  class Authro(models.Model):
    name = models.CharField(max_length=20, null=False)
  
  
  class Book(models.Model):
    book_name = models.CharField(max_length=20, default='', db_index=True)
    book_authro = models.ForeignKey("Authro", on_delete=models.CASCADE)
  
  # 操作
  def pk():
    book = Book(book_name="pingfang")
    authro = Authro.objects.create(name="zhangsan")
    authro.save() # 先保存到数据库之中
  
    book.book_authro = authro
    book.save()
  
  def run():
    pk()
  
  ```

  ![image-20220619231404418](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202206192314546.png)

- 修改

  ```python
  def modi():
    obj = Book.objects.get(book_name="pingfang")
    obj.book_authro.name = 'zzzaaa'
    obj.book_authro.save() #先修改外键、存储
    obj.save()
  ```

- 反向引用

  ```python
  def get():
    authro = Authro.objects.first() # 获取第一个作者的所有文章
    obj = authro.book_set.all() # 模型名字小写_set
    for o in obj:
      print(o)
  def run():
    get()
  ```

  - related_name

    - 上面通过 authro.book_set.all()的方式访问所的文章，如果不想使用`模型名字_set`的方式来访问，在定义模型的时候就可以指定

      ```python
      class Book(models.Model):
        book_name = models.CharField(max_length=20, default='', db_index=True)
        # 传递方向参数，以后就可以使用authro.books进行访问
        book_authro = models.ForeignKey("Authro", on_delete=models.CASCADE,related_name='books') 
      ```

  - 如果不想使用反向引用，可以如下设置

    ```python
    class Book(models.Model):
      book_name = models.CharField(max_length=20, default='', db_index=True)
      # 传递方向参数，以后就可以使用authro.books进行访问
      book_authro = models.ForeignKey("Authro", on_delete=models.CASCADE,related_name='+')
    ```

#### 3.5.3.2  一对一

- `Django`为一对一提供了一个专门的`Field`叫做`OneToOneField`来实现一对一操作

- 应用场景：比如一个用户表和一个用户信息表。在实际网站中，可能需要保存用户的许多信息，但是有些信息是不经常用的。   如果把所有信息都存放到一张表中可能会影响查询效率，因此可以把用户的一些不常用的信息存放到另外一张表中我们叫做`UserExtension`。但是用户表`User`和用户信息表`UserExtension`就是典型的一对一了

  ```python
  class Authro(models.Model):
    name = models.CharField(max_length=20, null=False)
    
  class AuthroExc(models.Model):
    age = models.IntegerField(null=True) #保存不常用信息，一个作者的年龄是唯一的
    authro = models.OneToOneField("Authro",on_delete=models.CASCADE)
  ```

- 在AuthroExc 类模型上新增加了一个一对一的关系映射。其实底层是在AuthroExc表中增加了一个authro_id，来和表authro进行关联，并且这个外键数据在表中必须是唯一的，来保证一对一(**也就是说，在authroExc表中authro_id必须是唯一的**)

#### 3.5.3.3 多对多

- 应用场景：比如文章和标签的关系。一篇文章可以有多个标签，一个标签可以被多个文章所引用。因此标签和文章的关系是典型的多对多的关系。

- 实现方式：`Django`为这种多对多的实现提供了专门的`Field`。叫做`ManyToManyField`

  ```python
  # 标签
  class Tag(models.Model):
    name = models.CharField(max_length=20)
  
  # 书籍
  class Book(models.Model):
    book_name = models.CharField(max_length=20, default='', db_index=True)
    book_authro = models.ForeignKey("Authro", on_delete=models.CASCADE)
    tags = models.ManyToManyField("Tag")
  
  ```

- 在数据库层面，实际上Django为这种多对多的关系建立起了一个中间表，这个中间表分别定义了两个外键，引用到book和tag两张表的主键

  - throutgh 可以指定多对多的中间关键表名称


  ![image-20220619231421197](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202206192314328.png)

  - 插入


  ```python
  def many():
    tag = Tag.objects.create(name="bq")
    tag.save()
  
    authro = Authro.objects.create(name='tianqi')
    authro.save()
  
    book = Book(book_name="xusanduo") 
    book.book_authro = authro # 一对多可以直接添加
    book.save()
    book.tags.add(tag) # 多对多，需要创建完成之后再添加
  ```

  ![image-20220417235851483](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202206192314157.png)

- 查找

  ```python
  # 查询书名为 xusanduo 所有的标签
  def many_find():
    book = Book.objects.get(book_name="xusanduo")
    obj = book.tags.values_list()
    print(obj) # <QuerySet [(4, 'bq')]>
  
    #obj = book.tags.values()
    # <QuerySet [{'id': 4, 'name': 'bq'}]>
    
  # 查询标签为bq对应的书籍 (通过标签查数据)
  def many_find2():
    tag = Tag.objects.get(name='bq')
    obj = Book.objects.filter(tags=tag)
    print(obj) # <QuerySet [<Book: Book object (2)>]>
  ```

#### 3.5.3.4 反向引用

- 通过主键，来进行反向操作

  ```python
  # 方法1
  def get():
    authro = Authro.objects.first() # 获取第一个作者的所有文章
    obj = authro.book_set.all() # 模型名字小写_set
    for o in obj:
      print(o)
  def run():
    get()
  
  # 方法2
  def get():
    authro = Authro.objects.get(name="tianqi")  # 获取第一个作者的所有文章
    obj = Book.objects.filter(book_authro=authro)
    print(obj)
  ```

- related_name

  - 上面通过 authro.book_set.all()的方式访问所的文章，如果不想使用`模型名字_set`的方式来访问，在定义模型的时候就可以指定

    ```python
    class Book(models.Model):
      book_name = models.CharField(max_length=20, default='', db_index=True)
      # 传递方向参数，以后就可以使用authro.books进行访问
      book_authro = models.ForeignKey("Authro", on_delete=models.CASCADE,related_name='books') 
    ```

  - 如果不想使用方向引用，可以如下设置

    ```python
    class Book(models.Model):
      book_name = models.CharField(max_length=20, default='', db_index=True)
      # 传递方向参数，以后就可以使用authro.books进行访问
      book_authro = models.ForeignKey("Authro", on_delete=models.CASCADE,related_name='+') 
    ```

- related_query_name

  - 在查找数据的时候，可以使用`filter`进行过滤。使用`filter`过滤的时候，不仅仅可以指定本模型上的某个属性要满足什么条件，还可以指定相关联的模型满足什么属性	

    ```python
    def query():
      authro = Authro.objects.filter(book__book_name="pingfang") #查找数据为pingfang的作者
      print(authro)
      print(type(authro)) 
      
      # <QuerySet [<Authro: Authro object (3)>]>
      # <class 'django.db.models.query.QuerySet'>
    def run():
      query()
    ```

  - 同理想修改访问的名字，则使用related_query_name 进行修改

    ```python
      book_authro = models.ForeignKey("Authro", on_delete=models.CASCADE,related_query_name="books")
      # 可以使用books__book_name="pingfang" 的方式进行访问
    ```

# 四、交互接口详解

## 4.1 增

- 增加数据有两种方法
  - 一种是通过model类来创建一个对象，然后进行save
  - 另外一种是直接调用object.create(cls,**kwargs)

## 4.2 删

- 方法一

  - 获取对象后再删除

    ```python
    def ddel():
      Demo.objects.get(id=1).delete()
    def run():
      ddel()
    ```

- 方法二

  - 批量获取条件对象后进行删除

    ```python
    def ddel():
      Demo.objects.filter(str_field=1).delete()
    def run():
      ddel()
    ```

## 4.3 查

### 4.3.1基本查询

- get() 查询单一结果，如果不存在则抛出**模型类.DoesNotExist**异常,**如果存在返回的是一个对象**

  ```python
  def find():
    obj = Demo.objects.get(id=1)
    print(obj)
    print(type(obj))
    
    # 下面说明了返回的是一个对象
    # Demo object (1)
    # <class 'app_demo.models.Demo'>
  ```

- all() 返回所有的查询结果,**返回的是一个QuerySet,里面装的是所有对象**

  ```python
  def find():
    obj = Demo.objects.all()
    print(obj)
    print(type(obj))
  
    obj_1 = obj[0]
    print(obj_1)
  
  
    # <QuerySet [<Demo: Demo object (1)>, <Demo: Demo object (2)>, <Demo: Demo object (3)>]>
    # <class 'django.db.models.query.QuerySet'>
    # Demo object (1)
  ```

- count() 查询结果数量

  ```python
  def find():
    obj = Demo.objects.count()
    print(obj)
    print(type(obj))
    
    # 3
    # <class 'int'>
  
  ```

### 4.3.2过滤查询

- 实现SQL之中的where功能，包括

  - filter 过滤出多个结果
  - exclude 排除掉符合条件剩下的结果
  - get 过滤单一结果

- 语法

  - 过滤条件的使用，三者相同

  - 过滤语法表达

    ```python
    属性名称__比较运算符=值
    # 属性名称和比较运算符间使用两个下划线，所以属性名不能包括多个下划线
    ```

- 过滤条件

  - exact 相等

    ```python
    def find():
        obj = Demo.objects.filter(id__exact=1) # 属性名称 _ _ 过滤条件 = 值
      print(obj)
      print(type(obj))
    
      # < QuerySet[ < Demo: Demo object(1) >] > 
      # <class 'django.db.models.query.QuerySet'>
    ```

    - __exact 和 = 是等价的[gd](https://docs.djangoproject.com/en/dev/topics/db/queries/)

  - 模糊查询

    - **contains**：是否包含。*说明：如果要包含%无需转义，直接写即可*，**找不到不会报错，而是返回空的QuerySet**
  
      ```python
      def find():
        obj = Demo.objects.filter(demo_field__contains="asdasdac")
        print(obj)
        print(type(obj))
       
        # <QuerySet []>
        # <class 'django.db.models.query.QuerySet'>
      ```

    - **startswith、endswith：以指定值开头或结尾**

  - 空查询

    - **snull**：是否为null
  
      ```python
      def find():
        obj = Demo.objects.filter(demo_field__isnull=False)
        print(obj)
        print(type(obj))
        
        # <QuerySet [<Demo: Demo object (1)>, <Demo: Demo object (2)>, <Demo: Demo object (3)>]>  
        # <class 'django.db.models.query.QuerySet'>
      ```

  - 范围查询

    - in 是否包含在范围内，比如查询id为1或者3
  
      ```python
      def find():
        obj = Demo.objects.filter(id__in=[1,3])
        print(obj)
        print(type(obj))
      
        # <QuerySet [<Demo: Demo object (1)>, <Demo: Demo object (3)>]>  
        # <class 'django.db.models.query.QuerySet'>
      ```

  - 比较查询
  
    - **gt** ：大于 (greater then)
      **gte** ：大于等于 (greater then equal)
      **lt** ：小于 (less then)
      **lte** ：小于等于 (less then equal)
  
      ```python
      def find():
        obj = Demo.objects.filter(id__gt=2)
        print(obj)
        print(type(obj))
      
        # < QuerySet[ < Demo: Demo object(3) >]>
        # < class 'django.db.models.query.QuerySet'>
      ```

  - 日期查询

    - **year**、**month**、**day**、**week_day**、**hour**、**minute**、**second**：对日期时间类型的属性进行运算

      - 查询创建时间为2022年
  
        ```python
        def find():
          obj = Demo.objects.filter(create_time__year=2022)
          print(obj)
          print(type(obj))
        
          # < QuerySet[ < Demo: Demo object(1) >, < Demo: Demo object(2) >, < Demo: Demo object(3) >]>
          # <class 'django.db.models.query.QuerySet'>
         
        ```

      - 查询更新时间大于2022年
  
        ```python
        def find():
          obj = Demo.objects.filter(create_time__year__gt=2022)
          print(obj)
          print(type(obj))
        
          # <QuerySet []>
          # <class 'django.db.models.query.QuerySet'>
        ```

  - F对象

    - 之前的查询都是对象的属性与常量进行比较,两个属性之间的比较则可以使用F对象

    - 需要导入模块 `from django.db.models import F`

      - 比如下方，id和create_time 之间的比较
  
      ```python
      def find():
        obj = Demo.objects.filter(id__lt=F('create_time')) # 查询 id < create_time
        print(obj)
        print(type(obj))
      
        # <QuerySet [<Demo: Demo object (1)>, <Demo: Demo object (2)>, <Demo: Demo object (3)>]>
        # <class 'django.db.models.query.QuerySet'>
      ```

  - Q对象(**多个条件**)

    - 多个过滤器逐个调用，同SQL之中where部分的and关键字

    - 需要导入 `from django.db.models import Q`

    - Q对象可以使用&、|连接，&表示逻辑与，|表示逻辑或

    - Q对象前可以使用~操作符，表示非not
  
      ```python
      # 查询id>1 && 创建年份=2022,不使用Q对象有下面两种写法
        obj = Demo.objects.filter(id__gt=1).filter(create_time__year=2022)
        obj = Demo.objects.filter(id__gt=1,create_time__year=2022)
      # -> 如果 查询id>1 或者年份=2022 就只能使用Q对象了
      
      # 使用Q对象
      def find():
        # id>1 || 时间年份>2022
        obj = Demo.objects.filter(Q(id__gt=1) | Q(create_time__year__gt=2022))
        print(obj)
        print(type(obj))
      
        # <QuerySet [<Demo: Demo object (2)>, <Demo: Demo object (3)>]>
        # <class 'django.db.models.query.QuerySet'>
      
      ```

### 4.3.3 聚合函数

- 使用aggregate()过滤器调用聚合函数。聚合函数包括：**Avg**(平均)，**Count**(数量)，**Max**(最大)，**Min**(最小)，**Sum**(求和)，被定义在django.db.models中

- aggregate()**返回值是一个字典类型**，格式如下

  ```python
   {'属性名__聚合类小写':值}
   
  如:{'bread__sum':3}
  ```

  - 比如计算所有的id总和

    ```python
    from django.db.models import Sum
    def agg_func():
      obj = Demo.objects.aggregate(Sum('id'))
      print(obj)
      print(type(obj))
      
      # {'id__sum': Decimal('6')}
      # <class 'dict'>
    ```

- 使用count时直接调用，一般使用aggregate过滤器(详情看基本查询之中)

### 4.3.4 排序

- 使用order_by对结果进行排序

  ```python
  order_by('条件') # 升序
  order_by('-条件') # 降序
  ```

- 比如查找所有的对象，然后进行排序

  ```python
  def order():
    obj = Demo.objects.all().order_by("id")
    print(obj)
    print(type(obj))
  
    obj = Demo.objects.all().order_by("-id")
    print(obj)
    print(type(obj))
    
    # <QuerySet [<Demo: Demo object (1)>, <Demo: Demo object (2)>, <Demo: Demo object (3)>]>
    # <class 'django.db.models.query.QuerySet'>
    
    # <QuerySet [<Demo: Demo object (3)>, <Demo: Demo object (2)>, <Demo: Demo object (1)>]>
    # <class 'django.db.models.query.QuerySet'>
  ```

### 4.3.5 关联查询

- 一对多访问语法
  - 一模型类对象.`多对应的模型类名小写_set`
- 多对一访问语法
  - 多对应的模型类对象.`多对应的模型类中的关系类属性名`

## 4.4 改

- 方法一

  - 获取到对象后，对属性进行修改，然后save

    ```python
    def modify():
      obj = Demo.objects.get(id=1) # 获取对象
      obj.demo_field='ccc' # 进行修改
      obj.save()
    
    def run():
      modify()
    
    ```

- 方法二

  - 使用模型类update进行修改，返回受影响的行数

  - 这里必须使用filter，因为QuerySet才有update方法

    ```python
    def modify():
      Demo.objects.filter(str_field=1).update(demo_field="test")
    def run():
      modify()
    ```

- 只修改对象的字段的方法
  - ` obj.save(update_fields=['book_name'])`
  - `obj.update(num='11121')` 这里需要使用QuerySet的方法

# 五、查询集

## 5.1 查询集概念

- **如果返回的查询集为空，再对其进行过滤器操作时，不会报错**

- Django的ORM也存在查询集的概念，也称为查询结果集QuerySet，表示从数据库中获取的对象集合

- 当调用下列方法时，返回的就是QuerySet查询集

  - all() 返回所有的数据
  - filter() 返回满足条件的数据
  - exclude() 返回满足条件之外的数据
  - order_by() 对结果进行排序

- 对查询集可以再次调用过滤器进行过滤

  ```python
  Demo.objects.all().order_by("-id")
  ```

- 从SQL角度来看，查询集与select等价，过滤器就好比where,order by子句
- 判断一个查询集中是否有数据
  - exists()：判断查询集中是否有数据，如果有则返回True，没有则返回False

## 5.2 特性

### 5.2.1 惰性执行

- 创建查询集不会访问数据库，直到调用数据时，才会访问数据库，调用数据的情况包括迭代、序列化、与if合用

  ```python
  def query():
    qs = Demo.objects.all() # 此时只是创建了一个查询集，没有访问数据库
    for q in qs:
      print(q.create_time) # 这里才会去进行数据库查询
  ```

### 5.2.3 缓存

- 使用同一个查询集，第一次使用会发生数据库的查询，然后Django会把结果缓存下来，再次使用这个查询集时会使用缓存的数据，减少了数据库的查询次数

### 5.2.4 限制查询集

- 可以对查询集进行取下标和切片操作，等同于sql之中的limit和offser子句
- 对查询集进行切片后返回一个新的查询集，不会立即执行查询
- 如果获取一个对象，直接使用[0]，等同于[0:1].get()，但是如果没有数据，[0]引发IndexError异常，[0:1].get()如果没有数据引发DoesNotExist异常

### 5.2.4 多条件查询代码写法

- [字典筛选](https://blog.csdn.net/Sean_TS_Wang/article/details/118497884?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522166446753416800182186028%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fall.%2522%257D&request_id=166446753416800182186028&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~pc_rank_v39-1-118497884-null-null.142^v51^pc_rank_34_2,201^v3^control_1&utm_term=Django%20%E5%A4%9A%E6%9D%A1%E4%BB%B6%E6%9F%A5%E8%AF%A2%E6%80%8E%E4%B9%88%E5%86%99%E4%BB%A3%E7%A0%81&spm=1018.2226.3001.4187)

# 六、管理器Manager

## 6.1 是什么

- 管理器是Django的进行数据库操作的接口，每个Django模型都拥有至少一个管理器
- 没有定义模型类管理器时，Django会默认生成一个名为objects的管理器，它是 **models.Manager**类的对象

## 6.2 自定义管理器

- 一旦模型类自定义了管理器，Django不再生成默认管理器对象objects

  ```python
  from django.db import models
  
  # 自定义管理器
  # 还可以自己添加其它的方法
  class DemoManager(models.Manager):
    def all(self):
      return super().filter(id__gt=0)
  
  
  class Demo(models.Model):
    id = models.BigAutoField(primary_key=True)
    create_time = models.DateTimeField(auto_now_add=True)  # 创建记录之时会添加时间
    update_time = models.DateTimeField(auto_now_add=True)  # 更新记录之时，会改变时间
    demo_field = models.CharField(max_length=20, default='', db_index=True)
    str_field = models.CharField(max_length=20, default='', db_index=True)
  
    class Meta:
      db_table = 'demo_table'
      
    demo = DemoManager()
  
  
  # result
    
  def manage():
    obj = Demo.demo.all()
    print(obj) # <QuerySet [<Demo: Demo object (9)>, <Demo: Demo object (10)>, <Demo: Demo object (11)>]>
  
  def run():
    manage()
  
  # 自定义了管理器，原来的就不存在了
  Demo.objects.all() # AttributeError: type object 'Demo' has no attribute 'objects'
  ```



# 七、QuerySet Api接口

- [参考链接](https://blog.csdn.net/weixin_41924879/article/details/109162317?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522166667626116782388054259%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fall.%2522%257D&request_id=166667626116782388054259&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~pc_rank_34-1-109162317-null-null.142^v59^pc_new_rank,201^v3^control_1&utm_term=Django%20%E5%A6%82%E4%BD%95%E5%AE%9A%E4%B9%89QuerySet&spm=1018.2226.3001.4187)
- 接口分为链式和非链式，也就是得到的结果是queryset和model对象
- 常用接口有
  - `exists`判断数据是否存在
  - last 获取集合之中的最后一个对象





# 八、QuerySet 拼接

- QuerySet 拼接有两种方式

- 第一种是自带的方法，这种方法得到的还是queryset结果

  - 两个queryset进行`|`运算得到并集，自动去重
  - 两个queryset进行`&`计算得到交集

  ```python
  a1 = User.objects.filter(id__gt=8)
  a2 = User.objects.filter(id__lt=4)
  a3 = a1 | a2   
  a4 = a1 & a2
  ```




# 九、隔离级别

- Django 默认是读已提交。可通过如下方式设置成可重复读

  ```python
          'OPTIONS': {
              "init_command": "SET default_storage_engine=INNODB; SET foreign_key_checks = 0; SET names 'utf8';",
              "isolation_level": "repeatable read" # 设置为可重复读
          }
    }
  ```

  

# 参考链接

[增删查改参考链接](https://blog.csdn.net/yanpenggong/article/details/82316514?ops_request_misc=&request_id=&biz_id=102&utm_term=Django%E6%95%B0%E6%8D%AE%E5%BA%93%E6%93%8D%E4%BD%9C&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-1-82316514.nonecase&spm=1018.2226.3001.4187)

[字段类型](https://blog.csdn.net/carrousel0516/article/details/106563861)

[QuerySet](https://blog.csdn.net/u012052168/article/details/89156021?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522165001262716782248573268%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=165001262716782248573268&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~baidu_landing_v2~default-4-89156021.142^v9^control,157^v4^control&utm_term=QuerySet&spm=1018.2226.3001.4187)

[外键链接](https://www.cnblogs.com/xingxia/p/django_model_foreignKey.html)
