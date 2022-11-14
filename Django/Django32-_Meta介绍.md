# 一、基本介绍

- `_meta`的API是Django的ORM系统的核心接口。提供了Django模块(比如查询、验证灯模块)之间的沟通能力
- API提供了两种能力
  - 返回当前Model之中所有字段的实例
  - 根据字段名称返回Model对应的字段的实例

# 二、提供Api

-  `get_field()`: 通过属性名称得到单个属性类型的实例
- `get_fields()`: 得到模型的所有字段的元组，并且可以通过参数控制返回哪些字段 
  - include_parents默认为true，如果为false则不搜索父类的字段
  - include_hidden默认为false，如果为True则会返回关联的字段否则只返回自身字段(比如多对多的键和外键)

- 测试代码

  ```python
  def test4(request):
    """
    测试Django的_meta提供的能力
    get_field(): 通过属性名称得到单个属性类型的实例
    
    get_fields(): 得到模型的所有字段的元组，并且可以通过参数控制返回哪些字段 
      include_parents默认为true，如果为false则不搜索父类的字段
      include_hidden默认为false，如果为True则会返回关联的字段否则只返回自身字段(比如多对多的键和外键)
    
    """
    ops = Book._meta
    print("得到属性的类型：", type(ops.get_field('book_name'))) # 得到属性的类型： <class 'django.db.models.fields.CharField'>
    
    print(ops.get_fields(include_hidden=True))
    print("------:",ops.concrete_fields)
    print("≠≠≠≠≠≠:",ops.private_fields)
    print("-=-=-=-:",ops.many_to_many)
    return  HttpResponse("success!!!")
  ```

  