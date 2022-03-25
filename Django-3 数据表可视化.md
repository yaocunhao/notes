# 一、为什么需要

- Django 的 ORM 模块提供了丰富的 API 用于实现对 Model 的增删改查，但是对于 Web 站点的管理运营人员来说，学习它们的成本较高，且误操作的可能性较大。当然开发人员自己也可以去构建管理界面，但是当系统越来越复杂，Model 越来越多的时候， 就会增加很多重复性的工作

# 二、操作

- 创建用户 `python manage.py createsuperuser --username=admin --email=admin@163.com`

# 三、将Model注册到管理后台

- 也就是通过管理页面就可以实现对数据库的快捷管理

- 注册过程

  - 在创建的App的admin.py文件中，将自定义的Model注册到管理后台

    ```python
    from django.contrib import admin #Django自动在admin.py文件中导入
    from index.models import Book, Author,UserInfo #这个需要我们自己导入相应的模型类（数据表）
    admin.site.register([Book,Author,UserInfo])
    ```

  - 注册命令

    - admin.site.register([Book,Author,UserInfo]) 方法实现多个模型类的注册
    - admin.site.register(Book) 实现单个模型类的注册![image-20220320225437581](https://raw.githubusercontent.com/yaocunhao/picture/main/image-20220320225437581.png)

# 四、django_admin_log数据表

- Admin 应用在数据库迁移的过程中只创建了 django_admin_log 一张表 ，用于记录通过管理后台完成的对 Model 的增删改操作

  ```sql
  CREATE TABLE `django_admin_log` (
    `id` int(11) NOT NULL AUTO_INCREMENT,
    `action_time` datetime(6) NOT NULL,
    `object_id` longtext,
    `object_repr` varchar(200) NOT NULL,
    `action_flag` smallint(5) unsigned NOT NULL,
    `change_message` longtext NOT NULL,
    `content_type_id` int(11) DEFAULT NULL,
    `user_id` int(11) NOT NULL,
    PRIMARY KEY (`id`),
    KEY `django_admin_log_content_type_id_c4bce8eb_fk_django_co` (`content_type_id`),
    KEY `django_admin_log_user_id_c564eba6_fk_auth_user_id` (`user_id`),
    CONSTRAINT `django_admin_log_content_type_id_c4bce8eb_fk_django_co` FOREIGN KEY (`content_type_id`) REFERENCES `django_content_type` (`id`),
    CONSTRAINT `django_admin_log_user_id_c564eba6_fk_auth_user_id` FOREIGN KEY (`user_id`) REFERENCES `auth_user` (`id`)
  ) ENGINE=InnoDB DEFAULT CHARSET=latin1;
  ```

  - id 是自增的主键字；
  - action_time：datetime 类型，保存操作发生的日期和时间；
  - object_id：longtext 类型，保存修改对象的主键；
  - object_repr：varchar 类型，保存修改后的对象执行 repr 函数的值，repr 是 Python 的内置函数，用于将对象转换为字符串；
  - action_flag：无符号 smallint 类型，用于记录操作类型 ADDITION（值为1，表示添加）、CHANGE（值为 2，表示更新）、DELETION（值为 3，表示删除）；
  - change_message：longtext 类型，用于保存修改对象的详细描述；
  - content_type_id：int 类型，外键关联 ContentType 对象；

  - user_id：int类型，外键关键User对象（默认值），记录执行操作的用户