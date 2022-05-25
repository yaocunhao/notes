

# 一、User对象

- [`User`](https://docs.djangoproject.com/en/2.0/ref/contrib/auth/#django.contrib.auth.models.User)对象是认证系统的核心。它们通常代表与您的站点交互的人，并用于启用诸如限制访问、注册用户配置文件、将内容与创建者关联等
- 在 Django 的身份验证框架中只存在一类用户，即[`'superusers'`](https://docs.djangoproject.com/en/2.0/ref/contrib/auth/#django.contrib.auth.models.User.is_superuser)管理员[`'staff'`](https://docs.djangoproject.com/en/2.0/ref/contrib/auth/#django.contrib.auth.models.User.is_staff)用户只是用户对象特殊属性集，而不是不同类别的用户对象
- 默认用户的主要属性
  - [`username`](https://docs.djangoproject.com/en/2.0/ref/contrib/auth/#django.contrib.auth.models.User.username)
  - [`password`](https://docs.djangoproject.com/en/2.0/ref/contrib/auth/#django.contrib.auth.models.User.password)
  - [`email`](https://docs.djangoproject.com/en/2.0/ref/contrib/auth/#django.contrib.auth.models.User.email)
  - [`first_name`](https://docs.djangoproject.com/en/2.0/ref/contrib/auth/#django.contrib.auth.models.User.first_name)
  - [`last_name`](https://docs.djangoproject.com/en/2.0/ref/contrib/auth/#django.contrib.auth.models.User.last_name)

# 二、用户操作

## 2.1 普通用户的创建

- 使用create_user() 辅助函数

  ```python
  from django.contrib.auth.models import  User
  def create_user():
  	# 创建普通用户，存储到auth_user 库之中
  	ret  = User.objects.create_user(username='user_test',password='123456',email='123@169.com')
  	print(type(ret)) # <class 'django.contrib.auth.models.User'>
  	print(ret) # user_test
  ```

## 2.2 超级用户的创建

- `python manage.py createsuperuser`
  - 提示输入账号、密码、email

## 2.3 密码的修改

- Django不会存储原始密码，而是存储一个hash值

- 修改密码的方式

  - 命令行修改：`manage.py changepassword *username*`

  - 编程修改

    ```python
    >>> from django.contrib.auth.models import User
    >>> u = User.objects.get(username='john')
    >>> u.set_password('new password')
    >>> u.save()
    ```

## 2.4 验证用户

- 用于[`authenticate()`](https://docs.djangoproject.com/en/2.0/topics/auth/default/#django.contrib.auth.authenticate)验证一组凭据。它将凭证作为关键字参数，`username`对于 `password`默认情况，针对每个 [身份验证后端](https://docs.djangoproject.com/en/2.0/topics/auth/customizing/#authentication-backends)[`User`](https://docs.djangoproject.com/en/2.0/ref/contrib/auth/#django.contrib.auth.models.User)检查它们，如果凭证对后端有效，则返回一个 对象。如果凭据对任何后端都无效，或者后端引发[`PermissionDenied`](https://docs.djangoproject.com/en/2.0/ref/exceptions/#django.core.exceptions.PermissionDenied)，则返回`None`
- request是一个可选的参数，通过HttpRequest 传递**authenticate()**

# 三、权限和授权

## 3.1 基本概念和操作

- Django 带有一个简单的权限系统。它提供了一种将权限分配给特定用户和用户组的方法。可以在管理站点使用，也可以在代码之中使用它

- 按照对象类型设置权限

  - 只有具有该类型 add/change/delete权限的用户，才能对该类型对象执行 add/cahnge/delete 操作

- 按照对象实例设置权限

  - [`ModelAdmin`](https://docs.djangoproject.com/en/2.0/ref/contrib/admin/#django.contrib.admin.ModelAdmin)，可以为相同类型的不同对象实例自定义权限
  - 通过使用类提供的 [`has_add_permission()`](https://docs.djangoproject.com/en/2.0/ref/contrib/admin/#django.contrib.admin.ModelAdmin.has_add_permission), [`has_change_permission()`](https://docs.djangoproject.com/en/2.0/ref/contrib/admin/#django.contrib.admin.ModelAdmin.has_change_permission)和 [`has_delete_permission()`](https://docs.djangoproject.com/en/2.0/ref/contrib/admin/#django.contrib.admin.ModelAdmin.has_delete_permission)按照特定对象设置权限

- User对象还有两个多对多字段

  - `groups`和`user_permissions`. [对象可以像任何其他Django 模型](https://docs.djangoproject.com/en/2.0/topics/db/models/)[`User`](https://docs.djangoproject.com/en/2.0/ref/contrib/auth/#django.contrib.auth.models.User)一样访问它们的相关对象

    ```python
    myuser.groups.set([group_list])
    myuser.groups.add(group, group, ...)
    myuser.groups.remove(group, group, ...)
    myuser.groups.clear()
    myuser.user_permissions.set([permission_list])
    myuser.user_permissions.add(permission, permission, ...)
    myuser.user_permissions.remove(permission, permission, ...)
    myuser.user_permissions.clear()
    ```

## 3.2 默认权限

- 当在`setting`文件中的`INSERTALLED_APPS`之中添加了`django.contrib.auth`，那么它会自动为每个App模块添加，增加、删除、修改权限

- 当执行迁移命令`migrate`时，会默认为`INSTALLED_APPS`之中已经存在的App和正在创建的App添加默认权限

- 如果一个model定义在`INSTALLED_APPS`之外，那么必须声明它属于那个App，在Meta的选项中声明方式为：`app_label = xxxdemo`

  - 假设app_lable  为demo，demo中定义了类base，则验证权限的方式如下

    ```python
    user.has_perm('demo.add_base')
    user.app_perm.('demo.change_base')
    user.app_perm('demo.delete_base')
    ```

## 3.3  groups 权限

- [`django.contrib.auth.models.Group`](https://docs.djangoproject.com/en/2.0/ref/contrib/auth/#django.contrib.auth.models.Group)用于设置组权限，组内用户享有组的权限
- 一个用户可以属于多个组
- 除了权限之外，组还可以给用户赋予标签和一些扩展功能
  - 比如给一号组发送电子邮件，其余组不会收到。Vip能访问的内容，其余组的成员不可访问

## 3.4 以编程的方式创建权限

- 可以在`Meta`中定义权限，也可以使用代码直接创建权限
- 然后通过`User`中的 user_permissions 或者Group进行权限的分配

## 3.5 权限缓存

- 在创建权限验证后，会被缓存到[`ModelBackend`](https://docs.djangoproject.com/zh-hans/2.0/ref/contrib/auth/#django.contrib.auth.backends.ModelBackend)之中，这对于请求-响应周期是很友好的，因为权限通常不会在添加后立即进行查询
- 如果需要添加权限，并且立即进行查询，比如从进行测试的时候，最简单的办法就是从数据中重新获取用户
- 































# 一、auth应用模块

## 1.1 模块的作用

- 在设计开发任何一个站点的时候都需要有“用户”的概念，从用户的注册、登陆、退出或者注销，到用户的浏览、订阅等，这些都需要用户管理系统来实现
- Django提供的 auth 模块能够快速的实现用户模块的基本功能

## 1.2 auth 表

- 新建项目后，Django 就把 auth 模块的所有功能提供给了开发者使用，开发者可以调用相应的接口，实现不同的功能需求

- auth 模块定义了一张名叫 auth_user 的数据表，该表是 auth 模块的内建用户表，开发者调用 auth 模块的相应接口生成此表，auth_user 表的字段以及字段类型，如下所示

  ![image-20220520175800414](../../../Library/Application Support/typora-user-images/image-20220520175800414.png)

## 