# 一、前言

- Django 建造网站的基本途径： 建立视图和 URLConf 。 正如我们所阐述的，视图负责处理一些主观逻辑，然后返回响应结果。作为例子之一，我们的主观逻辑是要计算当前的日期和时间
- 在当代 Web 应用中，主观逻辑经常牵涉到与数据库的交互。**数据库驱动网站 在后台连接数据库服务器，从中取出一些数据，然后在 Web 页面用漂亮的格式展示这些数据库驱动网站 在后台连接数据库服务器，从中取出一些数据，然后在 Web 页面用漂亮的格式展示这些数据数据**。这个网站也可能会向访问者提供修改数据库数据的方法
- 许多复杂的网站都提供了以上两个功能的某种结合。例如 Amazon.com 就是一个数据库驱动站点的良好范例。**本质上，每个产品页面都是数据库中数据以 HTML格式进行的展现，而当你发表客户评论时，该评论被插入评论数据库中**
- **由于先天具备 Python 简单而强大的数据库查询执行方法，Django 非常适合开发数据库驱动网站**

# 二、如何嵌入数据库

- 在我们进行Context渲染的时候，通常会需要从数据库读取数据。不同的数据库的语法规则不同，如果在代码之中写死了语句，下次更换数据库将无法使用。如下代码就是封装前后的区别

  ```python
  #------封装前
  from django.shortcuts import render_to_response
  import MySQLdb
  
  def book_list(request):
      db = MySQLdb.connect(user='me', db='mydb', passwd='secret', host='localhost')
      cursor = db.cursor()
      cursor.execute('SELECT name FROM books ORDER BY name') #利用sql语句获取数据，一旦数据库更改，将无法使用
      names = [row[0] for row in cursor.fetchall()]
      db.close()
      return render_to_response('book_list.html', {'names': names})
   
  
  # 封装后----------
  from django.shortcuts import render_to_response
  from mysite.books.models import Book
  
  def book_list(request):
      books = Book.objects.order_by('name') #用封装好的接口获取数据
      return render_to_response('book_list.html', {'books': books})
  ```

  

# 三、MTV开发模式

- MVC 是一种软件开发的方法，它把代码的定义和数据访问的方法（model）与请求逻辑 （controller）还有用户接口（view）分开来
- 这种设计模式关键的优势在于各种组件都是 松散结合 的。这样，每个由 Django驱动 的Web应用都有着明确的目的，并且可独立更改而不影响到其它的部分。比如，开发者更改一个应用程序中的URL而不用影响到这个程序底层的实现。设计师可以改变 HTML 页面 的样式而不用接触 Python 代码。数据库管理员可以重新命名数据表并且只需更改一个地方，无需从一大堆文件中进行查找和替换
- Django 紧紧地遵循这种 `MVC` 模式，可以称得上是一种 `MVC` 框架。 以下是 Django 中 `M`、`V` 和 `C` 各自的含义：
  - M ，数据存取部分，由django数据库层处理，本章要讲述的内容。
  - V ，选择显示哪些数据要显示以及怎样显示的部分，由视图和模板处理。
  - C ，根据用户输入委派视图的部分，由 Django 框架根据 URLconf 设置，对给定 URL 调用适当的 Python 函数
-  由于 C 由框架自行处理，而 Django 里更关注的是模型（Model）、模板(Template)和视图（Views），Django 也被称为 *`MTV`* 框架 
  - 在 MTV 开发模式中：
    - M 代表模型（`Model`），即**数据存取层**。该层处理与数据相关的所有事务：如何存取、如何验证有效性、包含哪些行为以及数据之间的关系等。
    - T 代表模板(`Template`)，即表**现层。该层处理与表现相关的决定： 如何在页面或其他类型文档中进行显示**。
    - V 代表视图（`View`），即**业务逻辑层。该层包含存取模型及调取恰当模板的相关逻辑**。 你可以把它看作**模型与模板之间的桥梁。**

# 四、数据库的配置

- 记住这些理念之后，让我们来开始 Django 数据库层的探索。 首先，我们需要做些初始配置；我们需要告诉Django**使用什么数据库以及如何连接数据库**

- 数据库配置也是在Django的配置文件里，缺省是 `settings.py`

  ```python
  # 数据库配置
  DATABASES = {
      'default': {
          'ENGINE': 'django.db.backends.sqlite3',  # 告诉django使用那个数据库
          'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),  # 数据库的名称,对于sqlite需要完整路径
      }
  }
  ```

  - `DATABASE_NAME` 将数据库名称告知 Django
  - `DATABASE_USER` 告诉 Django 用哪个用户连接数据库
  - `DATABASE_PASSWORD` 告诉Django连接用户的密码
  - `DATABASE_HOST` 告诉 Django 连接哪一台主机的数据库服务器

- 测试数据库配置

  - 输入下面这些命令来测试你的数据库配置：我们可以在`mysite` 项目目录下执行上章所提到的`python manage.py shell`来进行测试。（我们上一章提到过在，`manager.py shell`命令是以正确Django配置启用Python交互解释器的一种方法。 这个方法在这里是很有必要的，因为Django需要知道加载哪个配置文件来获取数据库连接信息。）

    ```python
    >>> from django.db import connection
    >>> cursor = connection.cursor()
    
    # 如果没有显示什么错误信息，那么你的数据库配置是正确的
    ```

# 五、第一个应用程序

- 前言
  - 本章节 主要叙述了如何去操作数据库

## 5.1 基础概念

- 创建一个 Django app-**一个包含模型，视图和Django代码**，并且形式为独立Python包的完整Django应用
- `project` 和 `app` 之间到底有什么不同呢？
  - 它们的区别就是一个是配置另一个是代码：
    - 一个`project`包含很多个Django app以及对它们的配置
    - 技术上，`project`的作用是提供配置文件，比方说哪里定义数据库连接信息, 安装的app列表， `TEMPLATE_DIRS` ，等等
    - 一个`app`是一套Django功能的集合，通常包括模型和视图，按Python的包结构的方式存在
      - 例如，Django本身内建有一些`app`，例如注释系统和自动管理界面。 `app`的一个关键点是它们是很容易移植到其他`project`和被多个`project`复用。
  - 如果是一个简单的站点，那么我们不需要创建app，这一点在前面的视图函数里面已经证明了。在那些例子中，我们只是简单的创建了一个称为`views.py`的文件，编写了一些函数并在`URLconf`中设置了各个函数的映射。 这些情况都不需要使用`apps`。
  - 但是，系统对app有一个约定： **如果你使用了Django的数据库层（模型），你必须创建一个Django app。 模型必须存放在apps中。因此，为了开始建造我们的模型，我们必须创建一个新的app**
  - **app又称模型、数据库**

## 5.2 创建流程

- 在`mysite` 项目文件下输入下面的命令来创建`books` app：

  ```python
  python manage.py startapp books
  ```

- 这个命令并没有输出什么，它只在 `mysite` 的目录里创建了一个 `books` 目录。 让我们来看看这个目录的内容：

  ```python
  books/
      __init__.py
      models.py # 模型
      tests.py
      views.py # 视图
  ```

  - 这个目录包含了这个app的模型和视图

- Django提供了用python代码来操控sql的能力

- 我们把注意力放在一个基本的 `书籍/作者/出版商` 数据库结构上。我们来假定下面的这些概念、字段和关系：

  - 一个作者有`姓`，有`名`及`email地址`。

  - 出版商有`名称`，`地址`，`所在城市、省，国家`，`网站`。

  - 书籍有`书名`和`出版日期`。它有`一个或多个作者`（和作者是多对多的关联关系`[many-to-many]`），`只有一个出版商`（和出版商是一对多的关联关系`[one-to-many]`，也被称作外键`[foreign key]`）

  - 第一步是用Python代码来描述它们。 打开由`startapp` 命令创建的`models.py` 并输入下面的内容：

    ```python
    from django.db import models
    
    class Publisher(models.Model):#出版人
        name = models.CharField(max_length=30)#名字
        address = models.CharField(max_length=50)#地址
        city = models.CharField(max_length=60)#城市
        state_province = models.CharField(max_length=30)#省份
        country = models.CharField(max_length=50)#国家
        website = models.URLField() #网址
    
    class Author(models.Model):
        first_name = models.CharField(max_length=30)#名
        last_name = models.CharField(max_length=40) #姓
        email = models.EmailField() #email
    
    class Book(models.Model):
        title = models.CharField(max_length=100)  #书名
        authors = models.ManyToManyField(Author)	#作者
        publisher = models.ForeignKey(Publisher) #出版社
        publication_date = models.DateField() #出版日期
    ```

    - 每个数据模型都是 `django.db.models.Model` 的子类。**它的父类 `Model`含了所有必要的和数据库交互的方法，并提供了一个简洁漂亮的定义数据库字段的语法**。信不信由你，这些就是我们需要编写的通过Django存取基本数据的所有代码

    - **每个模型相当于单个数据库表**，每个属性也是这个表中的一个字段。 属性名就是字段名，它的类型（例如CharField）相当于数据库的字段类型（例如varchar）。例如，Publisher模块等同于下面这张表（用PostgreSQL的 `CREATE TABLE` 语法描述）：

      ```python
      # 创建一张表
      CREATE TABLE "books_publisher" (
          "id" serial NOT NULL PRIMARY KEY,
          "name" varchar(30) NOT NULL,
          "address" varchar(50) NOT NULL,
          "city" varchar(60) NOT NULL,
          "state_province" varchar(30) NOT NULL,
          "country" varchar(50) NOT NULL,
          "website" varchar(200) NOT NULL
      );
      ```

      事实上，正如过一会儿我们所要展示的，Django 可以自动生成这些 `CREATE TABLE` 语句。

## 5.3 模型安装

- 完成这些代码之后，现在让我们来在数据库中创建这些表。 要完成该项工作，第一步是在 Django 项目中 激活 **这些模型**。 **将 books app 添加到配置文件的已安装应用列表中即可完成此步骤**

- 再次编辑 settings.py 文件， 找到 INSTALLED_APPS 设置。 INSTALLED_APPS 告诉 Django 项目哪些 app 处于激活状态

  - BooksConfig类写在文件books/apps.py之中，所以他的点式路径为`books.apps.BooksConfig`

  ```python
  # INSTALLED_APPS 告诉 Django 项目哪些 app 处于激活状态
  INSTALLED_APPS = [
       'django.contrib.auth',  # 认证系统。
       'django.contrib.contenttypes',  # 内容类型的框架。
       'django.contrib.sessions',  # 会话框架
       'django.contrib.messages',  # 消息传递框架。
       'django.contrib.staticfiles',  # 管理静态文件的框架
    
       'books.apps.BooksConfig', #告诉Django books APP处于激活的状态
  ]
  ```

- 验证模型有效性

  - `validate` 命令检查你的模型的语法和逻辑是否正确。如果一切正常，你会看到`0 errors found`消息。如果出错，请检查你输入的模型代码。错误输出会给出非常有用的错误信息来帮助你修正你的模型。

  - 一旦你觉得你的模型可能有问题，运行 `python manage.py check` 。 它可以帮助你捕获一些常见的模型定义错误

  - `python manage.py check`。这个命令帮助你检查项目中的问题，并且在检查过程中不会对数据库进行任何操作。

    ```python
    #发生错误
    TypeError: __init__() missing 1 required positional argument: 'on_delete'
    
    # 外键错误
    django 升级到2.0之后,表与表之间关联的时候,必须要写on_delete参数,否则会报异常:
    publisher = models.ForeignKey(Publisher, on_delete=models.DO_NOTHING) #修改
    ```

- 模型确认没问题了，运行下面的命令来生成 `CREATE TABLE` 语句

  -  `python manage.py makemigrations books`
    - `books` 是app的名称
    - 通过运行 `makemigrations` 命令，Django 会检测你对模型文件的修改（在这种情况下，你已经取得了新的），并且把修改的部分储存为一次 *迁移*。
    - **迁移是 Django 对于模型定义（也就是你的数据库结构）的变化的储存形式** - 没那么玄乎，它们其实也只是一些你磁盘上的文件。如果你想的话，你可以阅读一下你模型的迁移数据，它被储存在 `books/migrations/0001_initial.py` 里。别担心，你不需要每次都阅读迁移文件，但是它们被设计成人类可读的形式，这是为了便于你手动修改它们
    - **实际上迁移就是将数据库里面的内容在django之中以python的语法呈现出来**

  - `sqlmigrate`命令接收一个迁移的名称，然后返回对应的 SQL(**将数据库做了些什么事情体现出来**)

    ```python
    # 命令 $ python manage.py sqlmigrate books 0001
    
    
    # --------输出-------
    BEGIN;
    --
    -- Create model Author #在数据库之中创建Author这张表
    --
    CREATE TABLE "books_author" ("id" integer NOT NULL PRIMARY KEY AUTOINCREMENT, "first_name" varchar(30) NOT NULL, "last_name" varchar(40) NOT NULL, "email" varchar(254) NOT NULL);
    --
    -- Create model Book
    --
    CREATE TABLE "books_book" ("id" integer NOT NULL PRIMARY KEY AUTOINCREMENT, "title" varchar(100) NOT NULL, "publication_date" date NOT NULL);
    CREATE TABLE "books_book_authors" ("id" integer NOT NULL PRIMARY KEY AUTOINCREMENT, "book_id" integer NOT NULL REFERENCES "books_book" ("id") DEFERRABLE INITIALLY DEFERRED, "author_id" integer NOT NULL REFERENCES "books_author" ("id") DEFERRABLE INITIALLY DEFERRED);
    --
    -- Create model Publisher
    --
    CREATE TABLE "books_publisher" ("id" integer NOT NULL PRIMARY KEY AUTOINCREMENT, "name" varchar(30) NOT NULL, "address" varchar(50) NOT NULL, "city" varchar(60) NOT NULL, "state_province" varchar(30) NOT NULL, "country" varchar(50) NOT NULL, "website" varchar(200) NOT NULL);
    --
    -- Add field publisher to book
    --
    ALTER TABLE "books_book" RENAME TO "books_book__old";
    CREATE TABLE "books_book" ("id" integer NOT NULL PRIMARY KEY AUTOINCREMENT, "title" varchar(100) NOT NULL, "publication_date" date NOT NULL, "publisher_id" integer NOT NULL REFERENCES "books_publisher" ("id") DEFERRABLE INITIALLY DEFERRED);
    INSERT INTO "books_book" ("id", "title", "publication_date", "publisher_id") SELECT "id", "title", "publication_date", NULL FROM "books_book__old";
    DROP TABLE "books_book__old";
    CREATE UNIQUE INDEX books_book_authors_book_id_author_id_8714badb_uniq ON "books_book_authors" ("book_id", "author_id");
    CREATE INDEX "books_book_authors_book_id_ed3433e7" ON "books_book_authors" ("book_id");
    CREATE INDEX "books_book_authors_author_id_984f1ab8" ON "books_book_authors" ("author_id");
    CREATE INDEX "books_book_publisher_id_189e6c56" ON "books_book" ("publisher_id");
    COMMIT;
    
    # 解释
    1、这个 sqlmigrate 命令并没有真正在你的数据库中的执行迁移 - 它只是把命令输出到屏幕上，让你看看 Django 认为需要执行哪些 SQL 语句。这在你想看看 Django 到底准备做什么，或者当你是数据库管理员，需要写脚本来批量处理数据库时会很有用
    
    ```

  - 再次运行 `migrage`命令，**在数据库里创建新定义的模型的数据表，也就是执行刚才呈现出来的SQL命令**

    ```python
    # 执行 python manage.py migrate
    
    # 输出-------
    Operations to perform:
      Apply all migrations: auth, books, contenttypes, sessions
    Running migrations:
      Applying contenttypes.0001_initial... OK
      Applying contenttypes.0002_remove_content_type_name... OK
      Applying auth.0001_initial... OK
      Applying auth.0002_alter_permission_name_max_length... OK
      Applying auth.0003_alter_user_email_max_length... OK
      Applying auth.0004_alter_user_username_opts... OK
      Applying auth.0005_alter_user_last_login_null... OK
      Applying auth.0006_require_contenttypes_0002... OK
      Applying auth.0007_alter_validators_add_error_messages... OK
      Applying auth.0008_alter_user_username_max_length... OK
      Applying auth.0009_alter_user_last_name_max_length... OK
      Applying books.0001_initial... OK
      Applying sessions.0001_initial... OK
    
    ```

    - 这个 [`migrate`](https://docs.djangoproject.com/zh-hans/2.0/ref/django-admin/#django-admin-migrate) 命令选中所有还没有执行过的迁移（Django 通过在数据库中创建一个特殊的表 `django_migrations` 来跟踪执行过哪些迁移）并应用在数据库上 - 也就是将你对模型的更改同步到数据库结构上。

      迁移是非常强大的功能，它能让你在开发过程中持续的改变数据库结构而不需要重新删除和创建表 - 它专注于使数据库平滑升级而不会丢失数据。改变模型需要这三步：

      - 编辑 `models.py` 文件，改变模型。
      - 运行 [`python manage.py makemigrations`](https://docs.djangoproject.com/zh-hans/2.0/ref/django-admin/#django-admin-makemigrations) 为模型的改变生成迁移文件。
      - 运行 [`python manage.py migrate`](https://docs.djangoproject.com/zh-hans/2.0/ref/django-admin/#django-admin-migrate) 来应用数据库迁移。

      数据库迁移被分解成生成和应用两个命令是为了让你能够在代码控制系统上提交迁移数据并使其能在多个应用里使用；这不仅仅会让开发更加简单，也给别的开发者和生产环境中的使用带来方便

## 5.4 初试API(基本数据的访问)

- 比如想要访问一些数据运行 `python manage.py shell`

  - 当你使用`Django modle API`创建对象时Django并未将对象保存至数据库内，除非你调用`save()` 方法

  ```python
  >>> from books.models import Publisher #导入Publisher 模型类也就是导入数据库表
  >>> p1 = Publisher(name='Apress', address='2855 Telegraph Avenue', #创建类的实例，也就是设置表的自读阿奴
  ...     city='Berkeley', state_province='CA', country='U.S.A.',
  ...     website='http://www.apress.com/')
  >>> p1.save() #调用对象的save方法，也就是将数据保存到数据库之中，django在后台会执行insert语句
  
  >>> publisher_list = Publisher.objects.all() #获取数据库中Publisher类的所有对象。这个操作的幕后，Django执行了一条SQLSELECT语句
  >>> publisher_list
  
  # 结果
  <QuerySet [<Publisher: Apress>, <Publisher: O'Reilly>, <Publisher: Apress>]>
             
             
  # ----------自测-------
  p2 = Publisher(name='滴滴', address='中关村壹号', city='北京', state_province='北京', country='中国',
   website='http://www.apress.com/')
  >>> publisher_list = Publisher.objects.all()
  >>> publisher_list
  
  #-------展示的就是Publisher的name字段
  <QuerySet [<Publisher: Apress>, <Publisher: O'Reilly>, <Publisher: Apress>, <Publisher: 滴滴>]>
  
             
  # 如果显示不对，在models的模型类之中添加 __str__(self)函数，用来显示有用的信息
  ```

  - 如果需要一步完成对象的创建与存储至数据库，就使用`objects.create()`方法
    - `p1 = Publisher.objects.create(......)`

## 5.5 插入和更新数据

- 接着前面的测试代码

  ```python
  p2 = Publisher(name='滴滴', address='中关村壹号', city='北京', state_province='北京', country='中国',
   website='http://www.apress.com/')
  >>> publisher_list = Publisher.objects.all()
  >>> publisher_list
  
  #接下来使用p2的save不再是创建新的记录而是修改记录的内容(也就是执行的是update语句，而不是insert语句)
  >>> p2.name='进行修改'
  >>> p2.save()
  >>> p=Publisher.objects.all()
  >>> p
  <QuerySet [<Publisher: Apress>, <Publisher: O'Reilly>, <Publisher: Apress>, <Publisher: 进行修改>]>
  
  ```

- 注意删除数据的时候，要显示的进行删除，就要要减少 rm -rf ./*的使用一般

## 5.6 查询数据库操作

### 5.6.1 返回单条查询数据

