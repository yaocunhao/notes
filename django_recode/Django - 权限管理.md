# 一、auth应用模块

## 1.1 模块的作用

- 在设计开发任何一个站点的时候都需要有“用户”的概念，从用户的注册、登陆、退出或者注销，到用户的浏览、订阅等，这些都需要用户管理系统来实现
- Django提供的 auth 模块能够快速的实现用户模块的基本功能

## 1.2 auth 表

- 新建项目后，Django 就把 auth 模块的所有功能提供给了开发者使用，开发者可以调用相应的接口，实现不同的功能需求

- auth 模块定义了一张名叫 auth_user 的数据表，该表是 auth 模块的内建用户表，开发者调用 auth 模块的相应接口生成此表，auth_user 表的字段以及字段类型，如下所示

  ![image-20220520175800414](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202206192305380.png)

  - is_superuser：布尔值，默认值是 False。标识是否是超级用户，代表用户拥有所有权限

  - username：用户名，具有唯一性限制，最大长度为150个字符，只可以包含字母、数字、@、.、+、-、_这些字符

  - password：密码，Django 并不会存储原始密码，其存储的实际是原始密码经过 Hash 散列处理之后的值
  - is_staff：布尔值，默认为 False。标识用户是否可以访问管理后台

  - is_active：布尔值，默认值是True。标识当前用户是否处于激活状态

## 1.3 auth 模块的其它作用

- 用户的登录

  - 用户的登录(login),退出(logout)功能，封装在django.contrib.auth之中
  - 用户权限系统封装在django.contrib.auth.models.Permission 中 ，可以对用户的权限进行增加、修改、删除
  - 用户组可以通过 from django.contrib.auth.models.Group 导入后来创建组或者删除组

- ```python
  user.user_permission.add(permission)#给某个用户权限添加权限
  group = Group.objects.create(name=group_name)#添加新的用户组
  group.save() #保存新建好的用户组
  group.delete()#删除用户组
  ```

## 1.4 auth总结

- 实现并维护了用户与用户组的增加、删除、更改功能
- 实现了用户权限与用户组权限的增加、删除、更改
- 实现了可以自定义用户权限与用户组权限功能
- 除了以上功能外，Django auth 模块还提供了权限验证等功能以及一些常用的方法。

# 二、Auth 用户与用户组

- 用户认证系统中定义了三个Model用来标识用户与用户的关系，分别是User,AnonymousUser,Group,他们定义的路径在`django/contrib/auth/models.py`之中

## 2.1 User用户模型

- 导入方式

  ```python
  from django.contrib.auth.models import User
  
  # 源码分析
  class UserManager(BaseUserManager):
      use_in_migrations = True
      def _create_user(self, username, email, password, **extra_fields):
          """
          创建并保存具有给定用户名、电子邮件和密码的用户。
          """
          if not username:
              raise ValueError('The given username must be set')
          email = self.normalize_email(email)
          #使用户名规范化调用normalize_username
          username = self.model.normalize_username(username)
          #新建user实例
          user = self.model(username=username, email=email, **extra_fields)
          #设置密码的方法
          user.set_password(password)
          user.save(using=self._db)
          return user
        
      def create_user(self, username, email=None, password=None, **extra_fields):
          #普通用户的is_staff和is_superuser都为False
          extra_fields.setdefault('is_staff', False)
          extra_fields.setdefault('is_superuser', False)
          return self._create_user(username, email, password, **extra_fields)
  ```

- 基础属性数据表：`auth_user `

- 关联关系

  - 从代码来看

    - 除了基础属性之外，User之中还定义了与Group与Permission(权限)之间的关联关系
    - 如下述代码，User继承了PermissionMixin

    ```python
    class PermissionsMixin(models.Model):
    group=models.ManyToManyField(Group,...)
    user_permission=models.ManyToManyField(Permission,...)
    ```

  - 从数据表来看

    - auth_user 关联  auth_user_groups 和 auth_user_user_permissions，即与组合权限关联了起来
    - [图解](https://boardmix.cn/app/editor/oAO6OEn7z-eqy062Y4m8pA)

## 2.2 AnonymouseUser 匿名用户模型

- 对于未登录的用户，request的user属性指向了AnonmouserUser 表示的匿名用户，类实现如下

  ```python
  class AnonymousUser:
      id = None
      pk = None
      username = ''
      is_staff = False
      is_active = False
      is_superuser = False
      _groups = EmptyManager(Group)
      _user_permissions = EmptyManager(Permission)
      
      # 方法
      def save(self):
      	raise NotImplementedError("Django doesn't provide a DB representation for AnonymousUser.")
  		
      def delete(self):
      	raise NotImplementedError("Django doesn't provide a DB representation for AnonymousUser.")
  		
      def set_password(self, raw_password):
      	raise NotImplementedError("Django doesn't provide a DB representation for AnonymousUser.")
  		
      def check_password(self, raw_password):
      	raise NotImplementedError("Django doesn't provide a DB representation for AnonymousUser.")
  ```

  - 匿名类的 is_staff 和 is_active 以及 is_superuser 都设置成为了 False
  - 方法都抛出了异常，所以没有实现任何方法

## 2.3 Group 组模型

### 2.3.1 组的概念

- 组是对用户进行分类的通用方法，以便将权限或其他标签应用到这些用户
- **用户可以属于任意数量的组**，组中的用户自动拥有授予该组的所有权限
- 除了权限之外，组还可以方便地对用户进行分类，以便对他们应用一些标签或扩展功能
  - 比如创建一个组，给该组的所有成员发送电子邮件

### 2.3.2 源码分析

```python
class Group(models.Model):
    name = models.CharField(_('name'), max_length=150, unique=True)
    permissions = models.ManyToManyField(
        Permission,
        verbose_name=_('permissions'),
        blank=True,
    )
    objects = GroupManager()
    class Meta:
        verbose_name = _('group')
        verbose_name_plural = _('groups')
    def __str__(self):
        return self.name
    def natural_key(self):
        return (self.name,)
```

- Group 用户组之定义了一个字段 name，代表用户组的名称而且必须具有唯一性，其最大字符长度为 150
- 它还定义与 Permission 模型之间多对多关联关系，那么它们之间就有有一张中间表即 auth_group_permissions

## 2.4 Auth 实现身份认证

### 2.4.1 什么是身份认证

- 从上述可知，Auth 应用一般用在用户的登录注册上，用于判断用户是否合法，从而可以帮助开发者快速的构建用户系统
- 那么 Auth 应用又是如何是实现用户的认证的呢？当给定相关的条件或属性时候，我们可以去获取用户对象这个过程就被称为用户认证，Django Auth 应用提供了一个用来认证的方法即 `authenticate` 方法用来实现用户的认证行为

### 2.4.2 认证过程

- authenticate 方法一般接受 username 与 password 作为参数，如果通过了认证，就返回认证的实例对象，否则就会返回 None

- `authenticate`位于 django/contrib/auth/__init__.py 文件中

  ```python
  def authenticate(request=None, **credentials):
      #__get_backends获取当前系统中定义的认证后端，并依次迭代(在setting之中填写的认证后端)
      for backend, backend_path in _get_backends(return_tuples=True):
          try:
              inspect.getcallargs(backend.authenticate, request, **credentials)
          except TypeError:
              #此后端不接受这些凭据作为参数。返回继续执行循环
              continue
          try:
               #通过当前的认证后端尝试获取 User，若获取不到就会抛出异常！
              user = backend.authenticate(request, **credentials)
          except PermissionDenied:
              #抛出异常Permission
              break
           #如果没有返回，继续执行下一个认证
          if user is None:
              continue
          #添加一个属性标志，代表后端认证成功
          user.backend = backend_path
          return user
      # 所提供的凭据对所有后端、触发信号无效
      user_login_failed.send(sender=__name__, credentials=_clean_credentials(credentials), request=request)
  ```

- 从上述代码可知，authenticate 方法通过系统中定义的认证后端来获取用户对象(**即setting中设置的**)，那么 Django 是如何获取认证后端的呢？

  ```python
  def _get_backends(return_tuples=False):
      backends = []
      #AUTHENTICATION_BACKENDS 定义了当前系统可以用的身份认证列表
      for backend_path in settings.AUTHENTICATION_BACKENDS:
          #加载后端
          backend = load_backend(backend_path)
          backends.append((backend, backend_path) if return_tuples else backend)
      #如果未定义后端列表抛出异常
      if not backends:
          raise ImproperlyConfigured(
              'No authentication backends have been defined. Does '
              'AUTHENTICATION_BACKENDS contain anything?'
          )
      return backends
  ```

- 如果`setting`中没有设置AUTHENTICATION_BACKENDS，会使用户 Django 框架默认的后端认认证，它位于 django/conf/global_settings.py 文件中

  ```python
  AUTHENTICATION_BACKENDS=['django.contib.auth.backends.ModelBackend']  #当前系统默认认证后端ModelBackend
  ```

### 2.4.3 Auth应用获取用户模型

- get_user_model 用于获取当前系统定义的“用户模型”

  ```python
  def get_user_model():
      """
      返回一个处于激活状态的 User
      """
      try:
          return django_apps.get_model(settings.AUTH_USER_MODEL, require_ready=False) # 获取用户认证模型
      except ValueError:
          raise ImproperlyConfigured("AUTH_USER_MODEL must be of the form 'app_label.model_name'")
      except LookupError:
          raise ImproperlyConfigured(
              "AUTH_USER_MODEL refers to model '%s' that has not been installed" % settings.AUTH_USER_MODEL
          )
  
  ```

- 使用实例如下

  ```python
  # 使用默认User model时
  >>> from django.contrib.auth import get_user_model
  >>> get_user_model()
  <class 'django.contrib.auth.models.User'>
  
  # 使用自定义User model时
  >>> from django.contrib.auth import get_user_model
  >>> get_user_model()
  <class 'xxx.models.NewUser'>
  # get_user_model()实际获取的是settings.AUTH_USER_MODEL指定的User model
  ```

- **自定义认证模型**

  - Django 允许在 settings.py 文件中定义 AUTH_USER_MODEL 覆盖默认的 auth.User，以满足特定项目的需求
  - 所以，ModelBacakend 的 authenticate 方法首先会通过 username 尝试获取 User 对象，再去校验密码是否正确以及 is_active 的状态，最后返回 User 对象或是返回 None。在 settings.py 配置 AUTH_USER_MODEL 格式如下所示：

  ```python
  #格式： "<app名>.<model名>"
  AUTH_USER_MODEL = "appname.NewUser" 
  
  #在models.py编写示例
  from django.conf import settings
  from django.db import models
  class Article(models.Model):
      author = models.ForeignKey(settings.AUTH_USER_MODEL)
      title = models.CharField(max_length=100)
  ```

  - 在 django/contrib/auth/__init__.py 文件中定义了用户的各种行为，例如，登录、退出、用户的认证等等，通过这些可见 Django 的强大所在，Auth 应用模块可以帮助开发者减少很多的工作量，而且 Auth 应用的源码可以给开发者提供诸多的用户系统重写思路

# 三、权限管理

## 3.1 基本概念

- 上面介介绍了用户、组、权限表之间的关系
- Django 默认为每一个模型表添加四个权限即查看、增加、更新、删除。那么这些又是怎么样实现的呢？下面来进行介绍

- 权限管理表`auth_permission`

  ```mysql
  mysql> desc auth_permission;
  +-----------------+--------------+------+-----+---------+----------------+
  | Field           | Type         | Null | Key | Default | Extra          |
  +-----------------+--------------+------+-----+---------+----------------+
  | id              | int(11)      | NO   | PRI | NULL    | auto_increment |
  | name            | varchar(255) | NO   |     | NULL    |                |
  | content_type_id | int(11)      | NO   | MUL | NULL    |                |
  | codename        | varchar(100) | NO   |     | NULL    |                |
  +-----------------+--------------+------+-----+---------+----------------+
  ```

  - name 表示权限的作用，字符最大长度为 255
  - conten_type_id 表示对model模型的一个标识id
  - content_type 表示与 ContentType 是外键关联关系，这张表主要用于记录 App 与 model 的信息,**表示这个permission是属于哪个app下的哪个models**
  -  codename 代表权限的名称，最多允许 100 个字符长度

- ```
  mysql> desc django_content_type;
  +-----------+--------------+------+-----+---------+----------------+
  | Field     | Type         | Null | Key | Default | Extra          |
  +-----------+--------------+------+-----+---------+----------------+
  | id        | int(11)      | NO   | PRI | NULL    | auto_increment |
  | app_label | varchar(100) | NO   | MUL | NULL    |                |
  | model     | varchar(100) | NO   |     | NULL    |                |
  +-----------+--------------+------+-----+---------+----------------+
  3 rows in set (0.01 sec)
  ```

- Permission 的 Model 在 Django 中的源码定义如下所示

  ```python
  class Permission(models.Model):
   name = models.CharField(_('name'), max_length=255)
      content_type = models.ForeignKey(
          ContentType,
          models.CASCADE,
          verbose_name=_('content type'),
      )
      codename = models.CharField(_('codename'), max_length=100)
  
      objects = PermissionManager()
      #元数据项
      class Meta:
          verbose_name = _('permission')
          verbose_name_plural = _('permissions')
          #联合唯一即联合约束
          unique_together = (('content_type', 'codename'),)
          ordering = ('content_type__app_label', 'content_type__model',
                      'codename')
  
      def __str__(self):
          return "%s | %s | %s" % (
              self.content_type.app_label,
              self.content_type,
              self.name,
          )
  ```

## 3.2  默认权限分类

- 权限系统提供了一种将权限分配给特定用户和用户组的方法主要通过 Options 类实现。Django 管理站点使用权限系统，但是在你自己的代码中也可以使用它。Django 管理站点使用的权限如下:
  - “添加”权限
    - 限制用户“添加”表单的能力和添加一个 Model 实例对象
  - “更改”权限
    - 限制用户更改的能力，能够修改 Model 的实例对象
  - “删除”权限
    - 限制删除对象的能力，删除 Model 实例对象
  - “查看”权限
    - 限制查看对象的能力，查看 Model 实例对象

- 权限的分配

  - **权限是针对对象类型的全局设置**，而不是针对特定对象实例
  - 定义了权限就可以将权限分配给用户或者用户组，给用户分配权限会关联到 auth_user_user_permission
  - 用户组会关联到 auth_group_permisssion
  - 如果要给对应的用户或用户组添加权限，那么这两张表就会相应的增加权限，Django 会默认添加四个权限，通过查看 auth_permission 表可以得知 user 权限如下：

  ```mysql
  mysql> select * from auth_permission where content_type_id=4;
  +----+-----------------+-----------------+-------------+
  | id | name 权限名称    | content_type_id 外键| codename 权限名称编码，也是外键    |
  +----+-----------------+-----------------+-------------+
  | 13 | Can add user    能添加user |               4 | add_user    |
  | 14 | Can change user 能改变user|               4 | change_user |
  | 15 | Can delete user |               4 | delete_user |
  | 16 | Can view user   |               4 | view_user   | # Django2.0 中没有这个 
  +----+-----------------+-----------------+-------------+
  4 rows in set (0.01 sec)
  ```

  - Model 内置的权限被定义在 Django `django.db.models.options.Options`模块之中

    - 在定义的 Options 类中包含了 default_permisssions 属性，它指定了这以上的四种权限，如下所示(注: Django 2.0前没有view权限)：

      ```python
      default_permissions = ('add', 'change', 'delete', 'view')
      ```

    - 这几个权限在实际的开发业务中，可以根据需求进行相应的设置，同时还可以应用到 Django 的后台管理系统中，实现对 Model 的操作，当然这需要用户有访问管理后台的权限。

## 3.3 查看用户或用户组权限

- 权限名一般由权限动作和模型名组成。以 user 应用为例，Django 为 User 模型自动创建的 4 个可选权限名分别为:

  - 查看用户(view)：user.view_article

  - 创建用户(add)：user.add_article

  - 更改用户(change)：user.change_article

  - 删除用户(delete)：user.delete_article

- 查看用户权限， user.has_perm() 方法来判断用户是否已经拥有相应权限
  - user_username.has_perm('user.add_article')
  - user_username.has_perm('user.change_article')
- 查看某个用户所在用户组的权限或某个用户的所有权限(包括从用户组获得的权限)，我们可以使用 get_group_permissions() 和 get_all_permissions() 方法
  - user_username.get_group_permissions() (User用户对象.方法)
  - user_username.get_all_permissions()

## 3.4 测试代码

```python
from django.contrib.auth.models import User
from django.contrib.auth.models import Group
from django.contrib.auth import authenticate

# 创建用户
def create_test():
  user = User.objects.create_user(username='test_user', password='123456',
                                  email='123@163.com')
  print(type(user), user)  # <class 'django.contrib.auth.models.User'> test_user


# 验证用户
def authenticate_test():
  user = authenticate(username='test_user', password='123456')
  print(type(user), user)  # <class 'django.contrib.auth.models.User'> test_user


# 组的演示
def group_test():
  # 创建一个组，并且将用户添加到组之中
  # 之后再给组设置权限即可

  group = Group.objects.create(name='test_group')
  user = User.objects.get(username='test_user')
  user.groups.add(group)
  user.groups.all()
# 通过上述的代码就将用户 user 加入到了组 reader 中，我们可以通过用户组权限再给这个组设置相应的权限，查看 auth_user_groups 表即可得到对应关系



# 权限查看
def permissions_test():
  user = User.objects.get(username='test_user')

  # 没有个该用户设置添加和修改日志的权限因此为False
  print(user.has_perm('user.add_log_enter')) # False
  print(user.has_perm('user.change_log_enter')) # False
  
  # 查看用户所在组、查看用户所有权限
  print(user.get_group_permissions()) # set()
  print(user.get_all_permissions()) # set()
def run():
  # create_test()
  # authenticate_test()
  # group_test()
  #permissions_test()


```



# 四、User、Group、Permission关联起来

- 在大多数情况下 Django 默认的权限管理，不能满足开发者的实际业务需求，这时候就需要添加自定义权限，Django 给开发者提供了不止一种的方法来完成自定义权限

  [参考链接](https://blog.csdn.net/qq_41475058/article/details/104556833?ops_request_misc=&request_id=&biz_id=102&utm_term=Django%20%E7%9A%84%E6%9D%83%E9%99%90%E6%98%AF%E5%AF%B9%E4%BB%80%E4%B9%88%E8%AE%BE%E7%BD%AE%E7%9A%84%EF%BC%9F&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-0-104556833.142^v10^pc_search_result_control_group,157^v12^control&spm=1018.2226.3001.4187)

## 4.1 实现添加自定义权限

### 4.1.1 Meta 属性中创建权限

- 将需要的权限添加在Model的Meta属性中，创建添加相应的权限

  ```python
  from django.db import models
  from django.contrib.auth import get_user_model
  
  class Movie(models.Model):
  	id = models.BigAutoField(primary_key=True)
  	name = models.CharField(max_length=10,default='')
  	
  	# 外键为settings.AUTH_USER_MODEL指定的User model
  	# 当前的数据删除，对应的外键数据也会被删除(on_delete=CASCADE)
  	# 外键为settings.AUTH_USER_MODEL指定的User model,实际上就是关联的auth_user 之中的用户id
  	author = models.ForeignKey(get_user_model(),on_delete=models.CASCADE) 
  
  	class Meta:
  		db_table = 'movie'
  		#第一个元素指定了权限的名称 codename，第二个元素指定了权限的 name(作用)
  		permissions=(
  			('make_movie', 'can make book '), #添加制作电影权限
  			)
  ```

- 下图展示数据库中权限表添加权限前后的差别

  ![image-20220525235543104](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202206192305461.png)

### 4.1.2 通过代码添加权限

- 在auth_test App的视图函数中编写代码

  ```python
  from django.contrib.auth.models import Permission, ContentType
  from auth_test.models import Movie
  from django.http import HttpResponse
  
  
  def add_permissions(request):
    #  返回给定模型的ContentType对象,即在数据表中插入一行(记录app和model)，然后作为外键用于权限创建
    # 表示这个权限属于那个app下的model
    # 创建 ContentType如果必要的话。查找被缓存，以便后续查找 对于相同的模型不要击中数据库
    content_type = ContentType.objects.get_for_model(Movie)
  
    # 创建权限
    permission = Permission.objects.create(name='can test permission',
                                           codename='test_movie',
                                           content_type=content_type)
    return HttpResponse('权限创建成功')
  
  ```

- 配置好url，然后通过游览器访问，效果如下![image-20220526001222653](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202206192305574.png)

## 4.2 授权与验证

- 当前auth_user下面的权限![image-20220526001906432](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202206192305993.png)

- ych是超级用户不需要授予权限，因此用test_user 来测试

### 4.2.1 用户权限管理操作

- **权限本身只是一个数据，必须和用户进行绑定，才能起到作用**User模型和权限之间的管理，可以通过以下几种方式来管理：
  - myuser.user_permissions.set(permission_list)：直接给定一个权限的列表。
  - myuser.user_permissions.add(permission,permission,…)：一个个添加权限。
  - myuser.user_permissions.remove(permission,permission,…)：一个个删除权限。
  - myuser.user_permissions.clear()：清除权限。
  - myuser.has_perm(’<app_name>.’)：判断是否拥有某个权限。权限参数是一个字符串，格式是app_name.codename。
  - myuser.get_all_permissons()：获取所有的权限

- **给用户添加、删除权限的过程其实就是修改 auth_user_user_permissions 表数据记录的过程**，它是 User 和 Permission 的多对多关联关系表

- 在操作用户权限的过程中，我们要给 Movie 的实例添加属性需要使用它的 user_permissions 属性，首先，我们来获取 User 对象和 Permission 对象实例，操作权限的代码分别如下所示：

  ```python
  from django.contrib.auth.models import User, Permission
  from django.contrib.auth.models import Group
  from django.contrib.auth import authenticate
  
  def permissions_manage():
    user = User.objects.get(username='test_user')  # 获取用户对象
    add_movie = Permission.objects.get(codename='add_movie')  # 获取‘add_movie’权限对象
    change_movie = Permission.objects.get(codename='change_movie')
    print(user.get_all_permissions())  # 查看实例对象所有权限若无任何返回值是空集合set
  
    user.user_permissions.set([add_movie])  # 将user的权限设置为当前权限值，之前权限的会自动去掉(也就是在auth_user_user_permissions中添加关联关系)
    
    print(user.get_all_permissions()) # {'auth_test.add_movie'} ,注意会有缓存问题，因此打印不及时
  
    user.user_permissions.add(change_movie)  # 在当前权限的基础新增权限
    print(user.get_all_permissions())
  
    user.user_permissions.add(add_movie, change_movie)  # 同时也可接受多个权限值
    print(user.get_all_permissions())
  
    user.user_permissions.remove(change_movie)  # 删除权限(在关系表中删除该关系)
    print(user.get_all_permissions())
  
    #user.user_permissions.clear()  # 清空所有权限
    print(user.get_all_permissions())
  
  ```

### 4.2.2 用户组权限管理操作

- 权限组管理接口

  ```python
  group.permissions.set([permission_list])
  group.permissions.add(permission, permission, ...)
  group.permissions.remove(permission, permission, ...)
  group.permissions.clear()
  ```

- 给用户组添加、删除权限的过程基本同 User 是类似的

  ```python
  def permissions_manage_group():
    # 从权限列表获取权限
    add_movie = Permission.objects.get(codename='add_movie')
    change_movie = Permission.objects.get(codename='change_movie')
  
    # 创建用户组
    group_movie = Group.objects.create(name="movie_group")  # 在auth_group中插入一行
    # 添加用户组权限 -> 在auth_group_premission表中建立起对应的关系
    group_movie.permissions.set([add_movie, change_movie])
  
    # 获取对象
    user = User.objects.get(username='test_user')
    # 将用户添加到组中
    user.groups.add(group_movie)
    # 打印所有权限 -> 用户自动获得组的权限
    print(user.groups.all()) # <QuerySet [<Group: movie_group>, <Group: test_group>]>
  ```

### 4.2.3 用户的权限校验

- 通过上面的介绍。我知道了如何对用户与用户组的权限进行操作

- 接下来，我们还要明白权限授予后，我们还要对其进行校验，校验成功的用户方可执行某项权限规定的操作

- 用户的校验可以使用 User 实例的 has_perm 或 has_perms 方法，han_perm 判断当前用户是否有某一项权限，而后者则表示用户是否同时拥有多个权限。格式如下，has_perm 中传递的权限格式为

  ```python
  has_perm('appname.codename(权限编码)')
  
  # 而 has_perms 在校验多个权限时，需要将 n 个权限放入列表中，如下所示：
  has_perms(["add_movie","change_movie"]) # 全部都验证成功才返回True
  ```

- 代码

  ```python
  # 注意：此时的用户中没有权限，而是用户组之中的权限(和上面的例子同数据)
  def permission_judge():
    user = User.objects.get(username='test_user')  # 获取用户
    print(user.has_perm('auth_test.add_movie'))  # True
    print(user.has_perms(["auth_test.add_movie", "auth_test.change_movie", "auth_test.make_movie"])) # False
    print(user.has_perms(["auth_test.add_movie", "auth_test.change_movie"])) # True
  ```

# 五、Django自定义认证后端实现多种登录方式验证

## 5.1 Django自带系统认证

- Django自带用户的认证需要通过 authenticate 方法实现，而该方法就是使用 Django 默认认证后端 ModeBackend 进行用户验证的，但这种验证只是**简单地比对数据库中存储的用户名和密码是否匹配一致**，这样就会导致在很多情况下不能满足实际的业务的需求。这个时候我们就可以自定义一个认证后端，来实现某些需求

- 从Django自带认证后端分析验证思路
  - **首先如何想要实现用户的认证必须先要获得用户对象**
  - **然后调用 authenticate 方法实现认证**
  - 所以可想而知，认证后端是实现了 get_user(获取用户) 和 authenticate(验证方法) 这两个方法的 Python 类。其中 authenticate 将用户身份凭据作为关键字参数
  - 所以实现自定义认证也需要实现这两个方法(get_user,authenticate)

## 5.2 简单的认证后端过程

### 5.2.1 代码实现

- 在auth_test 应用下新建 backends.py 文件，将其作为单独模块出来，定义如下代码：

  ```python
  # 使用邮箱和密码进行认证
  from django.contrib.auth.models import User
  class EmailBackend(object):
  
    # 验证模块
    def authenticate(self, request, **credentials):
      # 获取邮箱的认证信息即邮箱账号实例
      email = credentials.get('email', credentials.get('username'))
      try:
        user = User.objects.get(email=email) # 根据email 获取用户
      except Exception as error:
        print(error)
      else:
        # 检查用户密码
        if user.check_password(credentials["password"]): # 验证密码是否正确
          return user
  
    # 获取用户模块
    def get_user(self, user_id):
      try:
        return User.objects.get(pk=user_id) # 根据用户id，返回用户
      except Exception as e:
        print(e)
        return None
  ```

- 在setting配置文件中添加如下内容

  ```python
  AUTHENTICATION_BACKENDS = [
    'django.contrib.auth.backends.ModelBackend',
    'auth_test.backends.EmailBackend', # 自定义认证后端
  ]
  ```

  - 在验证的时候，会从上往下依次进行认证
  - 如果认证成功，则返回对应的后端

- 验证程序

  ```python
  def backends_test():
  
    user = authenticate(username='test_user',password='123456')  # 如果验证成功则获取test_user 用户
    print(user.backend) # django.contrib.auth.backends.ModelBackend，验证成功，返回django默认后端
  
    user = authenticate(email="123@163.com", password="123456") # 如果验证成功则获取test_user 用户
    print(user.backend) # auth_test.backends.EmailBackend，验证成功，返回自定义后端
  ```

- 源码分析

  ```python
  def _get_backends(return_tuples=False):
      backends = []
      for backend_path in settings.AUTHENTICATION_BACKENDS: # 从配置中获取后端
          backend = load_backend(backend_path)
          backends.append((backend, backend_path) if return_tuples else backend)
      if not backends:
          raise ImproperlyConfigured(
              'No authentication backends have been defined. Does '
              'AUTHENTICATION_BACKENDS contain anything?'
          )
      return backends
    
  def authenticate(request=None, **credentials):
  	
    # for循环验证后端中的配置文件
      for backend, backend_path in _get_backends(return_tuples=True): # 获取验证后端
          try:
              user = _authenticate_with_backend(backend, backend_path, request, credentials) # 进行验证
          except PermissionDenied:
              break
          if user is None:
              continue #  当前验证失败，继续循环验证
        
  ```

  

# 五、问题

- get_all_permissions() 获取权限落后