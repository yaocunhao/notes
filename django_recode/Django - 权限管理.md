# 一、auth应用模块

## 1.1 模块的作用

- 在设计开发任何一个站点的时候都需要有“用户”的概念，从用户的注册、登陆、退出或者注销，到用户的浏览、订阅等，这些都需要用户管理系统来实现
- Django提供的 auth 模块能够快速的实现用户模块的基本功能

## 1.2 auth 表

- 新建项目后，Django 就把 auth 模块的所有功能提供给了开发者使用，开发者可以调用相应的接口，实现不同的功能需求

- auth 模块定义了一张名叫 auth_user 的数据表，该表是 auth 模块的内建用户表，开发者调用 auth 模块的相应接口生成此表，auth_user 表的字段以及字段类型，如下所示

  ![image-20220520175800414](../../../Library/Application Support/typora-user-images/image-20220520175800414.png)

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

  - name 表示权限名称，字符最大长度为 255
  - content_type 表示与 ContentType 是外键关联关系，这张表主要用于记录 App 与 model 的信息
  -  codename 代表权限的名称编码值，最多允许 100 个字符长度

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

    - 在定义的 Options 类中包含了 default_permisssions 属性，它指定了这以上的四种权限，如下所示：

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
  - user_username.get_group_permissions()
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



# 四、权限管理自定义以及权限校验

- 在大多数情况下 Django 默认的权限管理，不能满足开发者的实际业务需求，这时候就需要添加自定义权限，Django 给开发者提供了不止一种的方法来完成自定义权限

## 4.1 实现添加自定义权限

### 4.1.1 Meta 属性中创建权限

- 将需要的权限添加在Model的Meta属性中，创建添加相应的权限

  ```
  
  ```

  