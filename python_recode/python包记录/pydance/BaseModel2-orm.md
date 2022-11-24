# 一、简单认识

- 内部的Config类设置为True，才可以使用from_orm进行任意对象的初始化

  ```python
  class Base_2:
    name = "q123"
    age = 123
    sex = "gril" # 多余的字段也没有什么关系
  
  class BaseOrm(BaseModel):
    name: str = None
    age: int = None
  
    class Config:
      orm_mode = True # 设置为true
  
  
  db_obj = Base.objects.first()  # 从数据库之中读取一个文档(获取一个对象)
  b = Base_2()
  
  bm_obj = BaseOrm.from_orm(b) # 用任意对象来初始化这个Base类
  print(bm_obj)
  ```

  