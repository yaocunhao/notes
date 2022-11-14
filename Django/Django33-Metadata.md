# 一、什么是元数据

- **关于数据的数据或者叫做用来描述数据的数据或者叫做信息的信息**



# 二、Django的元数据

- Django的元数据就是一个`Model`的内部类，然后给这个添加各种成员变量

## 2.1 常用的选项介绍

- [所有可以添加的成员变量选项](https://docs.djangoproject.com/en/3.2/topics/db/models/#meta-options)

- `abstract=True`：表示这是一个抽象基类
- `db_table`： 表示当前`model`使用的数据表