# 一、更改Django 项目的目录结构

- 配置文件的修改
  - 在manage.py 文件中进行修改自己可
- 更改model文件为文件夹
  - 在项目中创建一个SQL文件，记录需要创建的表的SQL命令即可



# 二、删除了迁移文件，怎么进行数据的迁移

- **确定你已经把migrations下的所有文件及模型对应的数据表都删了**
- 执行：`python manage.py makemigrations --empty 应用名`
- 执行：`python manage.py makemigrations`
- 执行：`python manage.py migrate`
- OK