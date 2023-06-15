# 一、基本使用方式

- 可以使用`validator`装饰器实现自定义验证和对象之间的复杂关系

- 验证器是“类方法”，因此它们收到的第一个参数值是`UserModel`类

- 第二个参数始终是要验证的字段值；它可以随意命名

- 您还可以将以下参数的任何子集添加到签名中（**名称必须匹配**）：

  - `values`: 包含任何先前验证字段的名称到值映射的字典
  - `config`: 模型配置
  - `field`：正在验证的字段的属性。对象类型是`pydantic.fields.ModelField`.
  - `**kwargs`：如果提供，这将包括上面未在签名中明确列出的参数
    - 比如如果没设置field将会 在kwargs之中

- 验证器应返回解析后的值或引发`ValueError`, `TypeError`, or `AssertionError` (`assert`可以使用语句。

  ```python
  def test2():
    """如何提供参数验证"""
    """通过validator 参数来进行校验"""
  
    class UserModel(BaseModel):
      name: str
      username: str
      password1: str
      password2: str
  
      @validator('name')
      def name_must_contain_space(cls, v, field): # v表示当前的装饰器中的名称key对应的值
        print(field) # field:当前字段的属性 name='name' type=str required=True
        if ' ' not in v:
          raise ValueError('must contain a space')
        return v.title()
  
      @validator('password2')
      def passwords_match(cls, v, values, **kwargs): 
        # values 表示当前已经存储的值的字典：{'username': 'scolvin', 'password1': 'zxcvbn'}
        # kwargs： 'field': ModelField(name='password2', type=str, required=True), 'config': <class '__main__.Config'>}
        if 'password1' in values and v != values['password1']:
          raise ValueError('passwords do not match')
        return v
  
      @validator('username')
      def username_alphanumeric(cls, v):
        assert v.isalnum(), 'must be alphanumeric'
        return v
  
    # 正确的格式
    user = UserModel(name='samuel colvin',
                     username='scolvin',
                     password1='zxcvbn',
                     password2='zxcvbn') # 两次密码相同
  	
    # 错误格式
    print(user)
    try:
      # 数据格式不正确，发生异常 ValidationError
      UserModel(
          name='samuel',
          username='scolvin',
          password1='zxcvbn',
          password2='zxcvbn2',
      )
    except ValidationError as e:
        print(e)
  ```



# 二、提前验证器和多参数验证器

- 通过传递多个字段名称，单个验证器可以应用于多个字段
- *也可以通过传递特殊值在所有*字段上调用单个验证器`'*'`
- 关键字参数`pre`将导致在其他验证之前调用验证器
- 通过`each_item=True`将导致验证器应用于单个值（例如`List`、`Dict`、`Set`等），而不是整个对象
  - 比如 字段 L=[1,2,34,5],  将会将L迭代进行验证
  - 如果将验证器与引用`List`父类上的类型字段的子类一起使用，则使用`each_item=True`将导致验证器不运行；相反，列表必须以编程方式迭代

```python
from typing import List
from pydantic import BaseModel, ValidationError, validator


class DemoModel(BaseModel):
  square_numbers: List[int] = []
  cube_numbers: List[int] = []

  # '*' is the same as 'cube_numbers', 'square_numbers' here:
  @validator('*', pre=True)  # * 接收任意值  pre 在其它验证器之前调用
  def split_str(cls, v):
    if isinstance(v, str):
      return v.split('|') # 进行拆分动作
    return v

  @validator('cube_numbers', 'square_numbers')  # : 通过传递多个字段名，应用于多个字段
  def check_sum(cls, v):
    if sum(v) > 42:
      raise ValueError('sum of numbers greater than 42')
    return v

  @validator(
      'square_numbers', each_item=True
  )  # 对于复杂类型来说，each_item=True将导致验证器应用于单个值，而不是整个对象 比如{"value":1,"value2":2}，将用于验证每个值
  def check_squares(cls, v):
    assert v**0.5 % 1 == 0, f'{v} is not a square number'
    return v

  @validator('cube_numbers', each_item=True)
  def check_cubes(cls, v):
    # 64 ** (1 / 3) == 3.9999999999999996 (!)
    # this is not a good way of checking cubes
    assert v**(1 / 3) % 1 == 0, f'{v} is not a cubed number'
    return v

  
# ============
# 测试 each_time
class User(BaseModel):
  name: List[int]

  @validator('name', each_item=True)
  def check_name(cls, v):
    if v != 1:
      print("name不等于空")
    return v
l = [1, 2, 34, 5]
b = User(name=l)
```



# 三、始终使用验证器

- 出于性能原因，默认情况下，**当未提供值时，不会为字段调用验证器**。然而，在某些情况下，始终调用验证器可能是有用的或需要的，**例如设置动态默认值**

- 设置always 则是始终调用验证器，**默认没有提供值的时候是不会调用字段验证器的**

  ```python
  from datetime import datetime
  
  from pydantic import BaseModel, validator
  
  
  class DemoModel(BaseModel):
      ts: datetime = None
  
      @validator('ts', pre=True, always=True)  # 没有设置值也会调用验证器
      def set_ts_now(cls, v):
          return v or datetime.now() # 动态的设置默认值
  
  
  print(DemoModel())
  #> ts=datetime.datetime(2022, 11, 17, 14, 53, 25, 613715)
  print(DemoModel(ts='2017-11-08T14:00'))
  #> ts=datetime.datetime(2017, 11, 8, 14, 0)
  
  ```



# 四、重复使用验证器

- 设置allow_reuse 参数可以像下面代码一样使用参数，极大的减少了代码的重复性

  ```python
  from pydantic import BaseModel, validator
  
  def check(v):
    if v != "123":
      raise ValueError("name != 123")
    return v # 需要进行返回
  
  class User1(BaseModel):
    name: str
    v = validator('name', allow_reuse=True)(check) # 设置allow_reuse字段可以采用这种方式进行参数的验证
  
  class User2(BaseModel):
    name: str
    v = validator('name', allow_reuse=True)(check) # 在前面需要使用v进行接收
    
  u1 = User1(name="123")
  print(u1)
  u2 = User2(name="123")
  print(u2)
  
  
  # 如果不设置的话，需要在每个类之中填写验证函数
  # 如下所示代码重复量会很多
  class User(BaseModel):
    name: str
  
    @validator("name")
    def check_name(cls, v):
      return check(v)
  ```

  

# 五、根验证器

- 根验证器是**对整体参数**进行验证，所以验证函数只接收values，表示所有传入的参数,也只能返回value
- pre参数则表示，是在接收参数之后(**其它验证器调用之后，包括内置验证器，内置验证器就是直接返回值**)，还是在接收参数之前进行校验

  - 之前表示在对象初始化的时候，对**所有传入的字段**进行校验

  - 之后则表示对象已经完成了初始化，然后再对**当前对象的字段**进行的校验

    ```python
    from pydantic import BaseModel, validator, root_validator
    
    # ——————————————————————————————在之后进行根验证器调用——————————————————————————————
    # class User(BaseModel):
    #   name: str
    #   age: str
    
    #   @validator('name', pre=True)
    #   def check_name(cls, v, values):
    #     print(f'check_name:{values}')
    #     return v
    
    #   @root_validator
    #   def check_age(cls, values):
    #     print(f'check_age:{values}')
    #     return values
    
    # User(name='123', age='456')
    # #check_name:{}
    # #check_age:{'name': '123', 'age': '456'}
    # ————————现象,结论：先调用的普通验证器，然后调用的根验证器
    
    
    
    # ——————————————————————————————在之前进行根验证器调用——————————————————————————————
    # class User(BaseModel):
    #   name: str
    #   age: str
    
    #   @validator('name', pre=True)
    #   def check_name(cls, v, values):
    #     print(f'check_name:{values}')
    #     return v
    
    #   @root_validator(pre=True)
    #   def check_age(cls, values):
    #     print(f'check_age:{values}')
    #     return values
    
    # User(name='123', age='456')
    #heck_age:{'name': '123', 'age': '456'}
    #check_name:{}
    # ————————现象,结论：先调用的根验证器，然后调用的普通验证器
    
    
    
    # ——————————————————————————————根验证器使用前面的参数(内部吞噬异常)——————————————————————————————
    # class User(BaseModel):
    #   name: str
    #   age: str
    
    #   @validator('name', pre=True)
    #   def check_name(cls, v, values):
    #     print(f'check_name:{values}')
    #     raise ValueError('name Error')
    #     return v
    
    #   @root_validator
    #   def check_age(cls, values):
    #     name = values['name']
    #     age = name
    #     return values
    
    # User(name='123', age='456')
    #name = values['name']
    #KeyError: 'name'
    # ————————现象,结论：前面出现的异常会被内部吞噬掉，不会抛出
    
    
    # ——————————————————————————————根验证器使用前面的参数——————————————————————————————
    class User(BaseModel):
      name: str
      age: str
    
      @validator('name', pre=True)
      def check_name(cls, v, values):
        print(f'check_name:{values}')
        raise ValueError('name Error')
        return v
    
      @root_validator(skip_on_failure=True)
      def check_age(cls, values):
        name = values['name']
        age = name
        return values
    User(name='123', age='456')
    # name Error (type=value_error)
    # ————————现象,结论：设置skip_on_failure=true， 前面出现的异常不会被内部吞噬掉，会抛出
    ```

    


```python
from pydantic import BaseModel, ValidationError, root_validator


class UserModel(BaseModel):
  username: str
  password1: str
  password2: str

  @root_validator # root_validator(pre=True) 会报错
  def check_card_number_omitted(cls, values):
    if "card_number" in values:
      raise ValueError('card_number in values!!')
    return values

  @root_validator
  def check_passwords_match(cls, values):
    pw1, pw2 = values.get('password1'), values.get('password2')
    if pw1 is not None and pw2 is not None and pw1 != pw2:
      raise ValueError('passwords do not match')
    return values


#print(UserModel(username='scolvin', password1='zxcvbn', password2='zxcvbn'))
#try:
#    UserModel(username='scolvin', password1='zxcvbn', password2='zxcvbn2')
#except ValidationError as e:

try:
  user = UserModel(username='scolvin',
                   password1='zxcvbn',
                   password2='zxcvbn',
                   card_number='1234')
except ValueError as e:
  print(e)

print(user)
```



# 六、Field Checks

- 未知

# 七、数据类验证器

- dataclass 数据类，和继承BaseModel 是一样的效果

  ```python
  from datetime import datetime
  
  from pydantic import validator
  from pydantic.dataclasses import dataclass
  
  
  @dataclass
  class DemoDataclass:
    ts: datetime = None
  
    @validator('ts', pre=True, always=True)
    def set_ts_now(cls, v):
      return v or datetime.now()
  
  
  print(DemoDataclass())
  #> DemoDataclass(ts=datetime.datetime(2022, 11, 17, 14, 53, 25, 624193))
  
  print(DemoDataclass(ts='2017-11-08T14:00'))  # 如果不设置
  #> DemoDataclass(ts=datetime.datetime(2017, 11, 8, 14, 0))
  ```

  

# 六、验证器依赖其它值时，字段顺序

- 在验证器中，顺序靠后的字段可以访问已经定义的字段(顺序靠前的)

- 所有带有注释的字段(无论是只有注释的还是带有默认值的)将在所有没有注释的字段之前
  - 注释就是类型提示
- 给所有字段都添加注释，则说明模型中定义的顺序就是字段的顺序



# 七、验证问题记录

- 引用前面的字段时，前面的字段出现校验错误不会直接抛出异常，而是被pydantic内部捕获了，这是pydantic内部机制的原因，解决办法：[解决办法](https://github.com/pydantic/pydantic/issues/3915)