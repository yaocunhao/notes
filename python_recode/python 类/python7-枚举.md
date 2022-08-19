# 枚举

- 语法

  - 如果想将一个类定义为枚举类，只需要令其继承自 enum 模块中的 Enum 类即可 `from enum import Enum`
  - 枚举类不能用来实例化对象

- 访问方式

  ```python
  from enum import Enum
  
  
  class Food(Enum):
      beef = 1
      chicken = 2
      apple = 3
  
  
  # 调用枚举成员的3种方式
  print(Food.apple) # Food.apple
  print(Food['chicken']) # Food.chicken
  print(Food(1)) # # Food.beef
  
  # 调用枚举成员的name 和 value
  print(Food.beef.name) # beef
  print(Food.beef.value) # 1
  
  # 遍历枚举成员的方式
  for food in Food:
      print(food)
  # Food.beef
  # Food.chicken
  # Food.apple
  ```

- 规则

  - 枚举成员之间不可以比大小，但可以用 == 或者 is 进行比较是否相等

  ```python
  # False
  # False
  print(Food.beef.name is Food.apple.name)
  print(Food.beef == Food.chicken)
  ```

  - 枚举类中各个成员的值，不能在类的外部做任何修改

  - 枚举类还提供了一个 __members__ 属性，该属性是一个包含枚举类中所有成员的字典，通过遍历该属性，也可以访问枚举类中的各个成员

    ```python
    for name, value in Food.__members__.items():
        print(name, ":", value)
        
    beef : Food.beef
    chicken : Food.chicken
    apple : Food.apple
    ```

  - 枚举类中各个成员必须保证 name 互不相同，但 value 可以相同

    - @unique 装饰器，这样当枚举类中出现相同值的成员时，程序会报 ValueError 错误

  - 直接使用Enum()函数创建枚举类
    - `Color = Enum("Color",('red','green','blue'))`	
    - Enum() 函数可接受 2 个参数，第一个用于指定枚举类的类名，第二个参数用于指定枚举类中的多个成员