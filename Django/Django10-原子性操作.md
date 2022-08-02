- Django 的原子性只保证数据库的原子性，不保证变量的原子性

  - 也就是说事务只针对数据库层面，不针对python层

- 

  ```python
  """目的：验证Django的中模块的原子性"""
  
  from test_app.models import Base, many
  from django.db import transaction
  
  
  def test_many(): # 在其中调用的函数，也可以保证其原子性
    m = many(age=11100)
    m.save()
  
  
  def test(data: dict):
    with transaction.atomic():
      data['name'] = '456'
      test_many()
      b = Base()
      b.name = 12
      b.save()
      #ret = 1 / 0
  
  
  def run():
    dict = {'name': '123'}
    try:
      test(dict)
    except Exception as error:
      print(error)
      print(dict)
      
      
      # division by zero
      # {'name': '456'} -》 可以传入的变量发生了改变,Django 不保证变量的原子性
      # 但是可以保证是否写入到数据库之
  ```

  



- 参数解析
  - 发布保存点sid，从创建保存点开始执行的数据库操作将成为可能回滚事务的一部分
  - using 参数是数据库的名称。如果using未提供参数，则使用"default"默认数据库
