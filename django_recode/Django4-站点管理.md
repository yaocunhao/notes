# 一、前言

- 对于某一类网站，管理界面是基础设施中非常重要的一部分。这是以网页和有限的可信任管理者为基础的界面，它可以让你添加，编辑和删除网站内容。一些常见的例子：你可以用这个界面发布博客，后台的网站管理者用它来润色读者提交的内容，你的客户用你给他们建立的界面工具更新新闻并发布在网站上，这些都是使用管理界面的例子
- 但是管理界面有一问题：**创建它太繁琐**。当你开发对公众的功能时，网页开发是有趣的，但是创建管理界面通常是千篇一律的。**你必须认证用户，显示并管理表格，验证输入的有效性诸如此类。这很繁琐而且是重复劳动**

- Django 在对这些繁琐和重复的工作进行了哪些改进？它用不能再少的代码为你做了所有的一切。Django中创建管理界面已经不是问题
- 这一章是关于**Django的自动管理界面**。这个特性是这样起作用的：它读取你模式中的元数据，然后提供给你一个强大而且可以使用的界面，网站管理者可以用它立即工作

# 二、django.contrib 包

- Django自动管理工具是`django.contrib`的一部分。`django.contrib`是一套庞大的功能集，它是Django基本代码的组成部分，Django框架就是由众多包含附加组件(`add-on`)的基本代码构成的。你可以把`django.contrib`看作是可选的Python标准库或普遍模式的实际实现。它们与Django捆绑在一起，这样你在开发中就不用“重复发明轮子”了

# 三、管理界面

## 3.1激活管理界面

- Django管理站点完全是可选择的，因为仅仅某些特殊类型的站点才需要这些功能。 这意味着你需要在你的项目中花费几个步骤去激活它

- 第一步，对你的settings文件做如下这些改变：

  - 按照2.0的教程实际上都不需要做什么

  > 1. 将`'django.contrib.admin'`加入setting的`INSTALLED_APPS`配置中（`INSTALLED_APPS`中的配置顺序是没有关系的,但是我们喜欢保持一定顺序以方便人来阅读）
  > 2. 保证`INSTALLED_APPS`中包含`'django.contrib.auth'`，`'django.contrib.contenttypes'`和`'django.contrib.sessions'`，Django的管理工具需要这3个包。(如果你跟随本文制作mysite项目的话，那么请注意我们在第五章的时候把这三项INSTALLED_APPS条目注释了。现在，请把注释取消。)
  > 3. 确保`MIDDLEWARE_CLASSES`包含`'django.middleware.common.CommonMiddleware'` 、`'django.contrib.sessions.middleware.SessionMiddleware'` 和`'django.contrib.auth.middleware.AuthenticationMiddleware'` 。(再次提醒，如果有跟着做mysite的话，请把在第五章做的注释取消。)



## 3.2创建管理员账号

- ```python
   python manage.py createsuperuser
   Username: admi
   Email address: admin@example.com
   password:*****
  ```

## 3.3 向管理页面之中加入应用

- 只需要再做一件事：我们得告诉管理，book 对象需要一个后台接口。打开 `books/admin.py` 文件，把它编辑成下面这样：

  ```python
  from django.contrib import admin
  from .models import Book, Publisher, Author
  
  admin.site.register(Book)
  admin.site.register(Publisher)
  admin.site.register(Author)
  ```

  - 这些代码通知管理工具为这些模块逐一提供界面![image-20220310172437275](../../Library/Application Support/typora-user-images/image-20220310172437275.png)

- 小结
  - 创建books文件
  - 在models之中写入三个结构体，实际上就是创建三张表
  - 使用命令，完成数据迁移，和在数据库之中创建对应的表格
  - 在setting文件之中的操作
    - DATABASES：填写数据库的名称和数据库的路径
    -  'books.apps.BooksConfig', :告诉Django books APP处于激活的状态
  - 管理则是在admin文件之中，将对应的表格映射上去

# 四、Admin是如何工作的

- 当服务启动时，Django从`url.py`引导URLconf，然后执行`admin.autodiscover()`语句。这个函数遍历`INSTALLED_APPS`配置，并且寻找相关的 `admin.py`文件。如果在指定的app目录下找到`admin.py`，它就执行其中的代码
- 应用程序`django.contrib.auth`包含自身的`admin.py`，所以Users和Groups能在管理工具中自动显示。其它的`django.contrib`应用程序，如`django.contrib.redirects`，其它从网上下在的第三方Django应用程序一样，都会自行添加到管理工具
- 综上所述，**管理工具其实就是一个Django应用程序，包含自己的模块、模板、视图和`URLpatterns`**
  - 你要像添加自己的视图一样，把它添加到`URLconf`里面。
  - 你可以在Django基本代码中的`django/contrib/admin`目录下，检查它的模板、视图和`URLpatterns`，但你不要尝试直接修改其中的任何代码，因为里面有很多地方可以让你自定义管理工具的工作方式

# 五、管理界面的操作

## 5.1 设置可选字段

![image-20220310174554940](../../Library/Application Support/typora-user-images/image-20220310174554940.png)

- 因此需要修改代码，告诉Django，作者的邮箱地址允许输入一个空值。所有字段都默认`blank=False`，这使得它们不允许输入空值

  ```python
  class Author(models.Model):
      first_name = models.CharField(max_length=30)
      last_name = models.CharField(max_length=40)
      email = models.EmailField(blank=True)
  ```

## 5.2 设置日期型和数字型字段可选

- 允许为空的设置，`blank=True`同样适用于日期型和数字型字段，但是需要介绍一些背景

  - SQL有指定空值的独特方式，它把空值叫做NULL，NULL不等于空字符串。这时就有一个问题了，**比如一个varchar字段，不可能一会是NULL一会是空字符串，这样就会产生一些歧义**
  - 为了消除歧义，Django生成的表的每个字段，都会显示的加上NOT NULL，**即空值不允许为NULL，只能为字符串，就如下代码所示**

  ```mysql
  CREATE TABLE "books_author" (
      "id" serial NOT NULL PRIMARY KEY,
      "first_name" varchar(30) NOT NULL,
      "last_name" varchar(40) NOT NULL,
      "email" varchar(75) NOT NULL
  )
  ;
  ```

  - 在这种情况下，当遇到日期和数字类型字段时就会发生一些麻烦。当你没有输入这两个字段时，它会默认的插入空字符串，而这两个类型是不支持字符串的。所以，**如果你想允许一个日期型（`DateField`、`TimeField`、`DateTimeField`）或数字型（`IntegerField`、`DecimalField`、`FloatField`）字段为空，你需要使用`null=True`和`blank=True`**

- 修改book模块，允许发布日期为空，修改如下

  ```python
  class Book(models.Model):
      title = models.CharField(max_length=100)
      authors = models.ManyToManyField(Author)
      # 使用外键定义一个关系，告诉django，每个book对象都关联到一个Publisher对象
      publisher = models.ForeignKey(Publisher, on_delete=models.DO_NOTHING)  # 修改
      publication_date = models.DateField(null=True, blank=True) #允许为空和空的时候填写NULL
  ```

  - 添加`null=True`比添加`blank=True`复杂。因为`null=True`改变了数据的语义，即改变了`CREATE TABLE`语句，把`publication_date`字段上的`NOT NULL`删除了

## 5.3 自定义字段标签

- 在显示的页面之中，每个字段都是从模块的字段名称生成的。规则很简单：用空格替换下划线；首字母大写。例如：Book模块中`publication_date`的标签是`Publication date`

- 然而，字段名称并不总是贴切的。有些情况下，你可能想自定义一个标签。你只需在模块中指定`verbose_name`

  - 在字段的约束处添加 verbose_name='更改的名称即可'，` email = models.EmailField(blank=True, verbose_name='e-mail' )`
  - 也可以简单的当做 **固定**位置的参数传递 `email = models.EmailField('e-mail',  blank=True)`
    - 但是这种固定位置的参数传递不适用与`ManyToManyField`和`ForeignKey`字段，因为它们第一个参数必须是模块类

  - Book修改后的展示 `publication_date = models.DateField('新的名字', null=True, blank=True)`![image-20220310183943966](../../Library/Application Support/typora-user-images/image-20220310183943966.png)

## 5.4 自定义ModelAdmi类

- 迄今为止，我们做的`blank=True`、`null=True`和`verbose_name`修改其实是模块级别，而不是管理级别的。 也就是说，这些修改实质上是构成模块的一部分，并且正好被管理工具使用，而不是专门针对管理工具的

- 除了这些，Django还提供了大量选项让你针对特别的模块自定义管理工具。这些选项都在`ModelAdmin classes`里面，这些类包含了管理工具中针对特别模块的配置

- 自定义列表

  - 在模型之中，自定义Author模块的列表中的显示字段，列表默认地显示查询对象中的`__str__`来同时显示作者的姓和名。

    ```python
     def __str__(self):
          return '%s %s' % (self.first_name, self.last_name)
    ```

    ![image-20220310184954644](../../Library/Application Support/typora-user-images/image-20220310184954644.png)

  - 我们可以在这基础上改进，添加其它字段，从而改变列表的显示。我们可以不必再去修改模型，而是通过自定义管理工具

  - 为了达到这个目的，我们将为`Author`模块定义一个`ModelAdmin`类。这个类是自定义管理工具的关键，其**中最基本的一件事情是允许你指定列表中的字段**。打开`admin.py`并修改：

    ```python
    #添加如下代码
    class AuthorAdmin(admin.ModelAdmin):
        list_display = ('first_name', 'last_name', 'email')
        
    admin.site.register(Author, AuthorAdmin)
    ```

    ![image-20220310191359886](../../Library/Application Support/typora-user-images/image-20220310191359886.png)

    - 我们新建了一个类`AuthorAdmin`，它是从`django.contrib.admin.ModelAdmin`派生出来的子类，保存着一个类的自定义配置，以供管理工具使用
    -  我们只自定义了一项：`list_display`，它是一个字段名称的元组，用于列表显示。当然，这些字段名称必须是模块中有的
    - 我们修改了`admin.site.register()`调用，在`Author`后面添加了`AuthorAdmin`。你可以这样理解：用`AuthorAdmin`选项注册`Author`模块
    - `admin.site.register()`函数接受一个`ModelAdmin`子类作为第二个参数。 如果你忽略第二个参数，Django将使用默认的选项。`Publisher`和`Book`的注册就属于这种情况![image-20220310191640506](../../Library/Application Support/typora-user-images/image-20220310191640506.png)

  - 添加快速查询栏

    - 向`AuthorAdmin`追加`search_fields`，如：

      ```python
      class AuthorAdmin(admin.ModelAdmin):
          list_display = ('first_name', 'last_name', 'email')
          search_fields = ('first_name', 'last_name')
      ```

​								![image-20220310191946146](../../Library/Application Support/typora-user-images/image-20220310191946146.png)

- 为`Book`列表页添加一些过滤器

  -  Django为日期型字段提供了快捷过滤方式，它包含：今天、过往七天、当月和今年

  ```python
  class BookAdmin(admin.ModelAdmin):
      list_display = ('title', 'publisher', 'publication_date') #添加列表栏
      list_filter = ('publication_date',) #添加日期过滤
  
  
  admin.site.register(Book, BookAdmin)
  ```

  ![image-20220310192539798](../../Library/Application Support/typora-user-images/image-20220310192539798.png)

  - 过滤器”同样适用于其它类型的字段，而不单是“日期型”（请在“布尔型” 和“外键” 字段上试试）。**当有两个以上值时，过滤器就会显示**	

  - 另外一种过滤日期的方式是使用`date_hierarchy`选项，如：

    ```python
    class BookAdmin(admin.ModelAdmin):
        list_display = ('title', 'publisher', 'publication_date')  # 第一行的标签栏
        list_filter = ('publication_date',)  # 粗略过滤
        date_hierarchy = 'publication_date'  # 较为精确过滤
        ordering = ('-publication_date',)  # 加-实现降序
    ```

    - 修改好后，页面中的列表顶端会有一个逐层深入的导航条， 它从可用的年份开始，然后逐层细分到月乃至日![image-20220310194001353](../../Library/Application Support/typora-user-images/image-20220310194001353.png)
      - 请注意，`list_filter`接收列表，date_hierarchy 接收的是字符串								

- 自定义编辑表单

  - 首先，我们先自定义字段顺序。默认地，表单中的字段顺序是与模块中定义是一致的。 我们可以通过使用`ModelAdmin`子类中的`fields`选项来改变它：

    - `fields`之中少了什么，代表什么选择不可被编辑

    ```python
    class BookAdmin(admin.ModelAdmin):
        list_display = ('title', 'publisher', 'publication_date')  # 第一行的标签栏
        list_filter = ('publication_date',)  # 粗略过滤
        date_hierarchy = 'publication_date'  # 较为精确过滤
        ordering = ('-publication_date',)  # 加-实现降序
        fields = ('authors', 'title', 'publisher')  # 改变表单显示的顺序 && 日期不可被编辑
    ```

    ![image-20220310200157398](../../Library/Application Support/typora-user-images/image-20220310200157398.png)

    - 当一个用户用这个不包含完整信息的表单添加一本新书时，Django会简单地将`publication_date`设置为`None`，以确保这个字段满足`null=True`的条件(日期栏被限制了，不能填入)

  - 让多选字段更好用`filter_horizontal`,**只能用在多字段上**

    ```python
        filter_horizontal = ('authors',)
    ```

    ![image-20220310200850144](../../Library/Application Support/typora-user-images/image-20220310200850144.png)

  - `raw_id_fields =`：下拉框变文本框，**只能用于包含外键的字段**

    - 管理工具使用“下拉框”来展现“外键”字段。但是，正如“多对多字段”那样，有时候你不想忍受因装载并显示这些选项而产生的大量开销。例如，我们的`book`数据库膨胀到拥有数千条`publishers`的记录，以致于`book`的添加页面装载时间较久，因为它必须把每一个`publishe`都装载并显示在“下拉框”中

      ```python
      raw_id_fields = ('publisher',)
      ```

      ![image-20220310201228338](../../Library/Application Support/typora-user-images/image-20220310201228338.png)

# 六、用户、用户组和权限