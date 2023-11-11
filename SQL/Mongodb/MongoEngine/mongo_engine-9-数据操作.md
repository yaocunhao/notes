- update 是否会触发数据库操作?

  ```python
  是会立即触发的
  
def test17():
  """
  测试update 是否会触发数据库操作
  """

  class Base2(Document):
    name = StringField()
    age = IntField()

  data = Base2.objects
  data.update(name='ccccccc')
  for i in range(100000000):
    print("update after....")
  data.update(age='asdas')
  
  ```

  