# 前言

- 在 pydantic 中定义对象的主要方法是通过模型（模型继承 BaseModel ）
- pydantic 主要是一个解析库，也就是说pydantic可以定义一个数据输出模型，而不是一个数据输入模型(验证)
- 虽然验证不是主要的目的，但是也可以通过自定义的方式来进行验证
  - 比如当定义的字段没有默认值时就是必须输入的
- 使用版本为：`1.9.1`


# 记录

- [参考链接](https://blog.csdn.net/IT_LanTian/article/details/123229717?ops_request_misc=&request_id=&biz_id=102&utm_term=python%20BaseModel&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-1-123229717.142^v65^pc_rank_34_queryrelevant25,201^v3^control_1,213^v2^t3_esquery_v2&spm=1018.2226.3001.4187)
- [gd](https://pydantic-docs.helpmanual.io/usage/exporting_models/#modeldict)

- 测试代码

  ```python
  from pydantic import BaseModel
  
  
  class User(BaseModel):
    id: int
    name = 'yoyo'
    value = {'a': 123, 'b': 456}
  
  
  user = User(id='123')
  print(type(user.id))  # <class 'int'>, 根据字段类型将str转化为int了
  print(user.__fields_set__)  # {'id'}, 获取必填字段
  
  # 将对象属性转换成字典格式输出
  print(user.dict())  # {'id': 123, 'name': 'yo yo'}
  print(dict(user))  # {'id': 123, 'name': 'yo yo'}
  
  # 将对象属性转换成json格式输出
  print(user.json())  # {"id": 123, "name": "yo yo"}
  
  # 输出所有的参数
  print(user.__fields__)  # {'id': ModelField(name='id', type=int, required=True), 'name': ModelField(name='name', type=str, required=False, default='yoyo')}
  
  # 判断是否为一个复杂的变量
  # 比如 id不是，value是的，因为value是一个字典
  print(user.__fields__['value'].is_complex())
  ```

  