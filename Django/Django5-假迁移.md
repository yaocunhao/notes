# 一、是什么

- 通常情况下，数据库中一张表只允许被一个model使用，这是因为在数据迁移的时候会进行自动检查
- 假迁移就是，**多个model**使用同一张数据表

# 二、应用场景

## 2.1 场景描述

- 比如，同一份代码，那么访问的数据库的内容是一致的，现在拷贝一份代码，并且进行一些修改，使得采用"相同的"代码，访问不同的数据

## 2.2 流程

- 下面两个项目采用的是同一个数据库的同一张表，如果不进行假迁移，那么后迁移的表会出现 `table exit`的异常

- 项目一

  ```python
  class Base(models.Model):
    create_time = models.DateTimeField(auto_now_add=True)
    class Meta:
      abstract = True
  
  class Common(Base):
    ploy_id = models.CharField(max_length=20,db_column='m_ploy_id',default="",blank=True,null=True) # db_colum 对字段重新命名
    class Meta:
      db_table = 'test_table'  
  ```

  - 执行迁移命令

  ```python
  # python manage.py makemigrations
  # python manage.py migrate
  ```

- 项目二

  ```python
  class Base(models.Model):
    create_time = models.DateTimeField(auto_now_add=True)
    class Meta:
      abstract = True
  
  class Common(Base):
    ploy_id = models.IntegerField(default=0)
  
    class Meta:
      db_table = 'test_table'
  ```

  - 执行**假迁移命令**

    ```python
    # python manage.py makemigrations
    # python manage.py migrate --fake
    ```

  - **注意事项**

    - 假迁移的字段并不会在数据库中自动生成，因此需要手动进行添加

## 2.3 效果展示

### 2.3.1 数据的插入

- 项目一

  ```python
  def fake():
    Common.objects.create(ploy_id='django_demo_342432')
  ```

- 项目二

  ```python
  def func():
    Common.objects.create(ploy_id= 1000)
  def run()
  	func2()
  ```

- **注意点**

  - 两个项目使用的字段都是ploy_id，但是项目一中实际上对其重命名为 `m_ploy_id`
  - 虽然操作的时候采用的字段相同，但是在数据库中是不同的

![image-20220619230326425](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202206192303491.png)

### 2.3.2  数据的查询

- 如下代码所示，证明了相同字段，其实访问的数据是不同的

  ```python
  # 项目一
  def func2():
    obj = Common.objects.filter(ploy_id__gt=0)
    print(obj) # <QuerySet [<Common: Common object (2)>, <Common: Common object (3)>]>
    
  # 项目二
   obj = Common.objects.filter(ploy_id__gt=0)
    print(obj) # <QuerySet [<Common: Common object (4)>]>
  
    
  # 同样的代码，访问的内容是不一样的
  ```

  