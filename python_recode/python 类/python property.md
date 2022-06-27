# 一、property() 函数

## 1.1 作用

- 在不破坏类的封装性的基础之上,继续使用`类名.属性名`的放方式进行成员的访问
- 也就是说，在定义实例变量的时候，**将其定义为私有变量，这样就无法直接进行访问了，从而不会破坏类的封装性**
- 如果将类的实例变量设置为私有成员，并且想在外部访问时，就需要提供访问的函数接口，`property`提供的便利就是还是可以通过`类名.属性名的方式访问`

## 1.2 语法和使用

- `属性名=property(fget=None, fset=None, fdel=None, doc=None)`

  - fget 参数用于指定获取该属性值的类方法
  - fset 参数用于指定设置该属性值的方法
  - fdel 参数用于指定删除该属性值的方法
  - 最后的 doc 是一个文档字符串，用于说明此函数的作用

- 在使用 property() 函数时，以上 4 个参数可以仅指定第 1 个、或者前 2 个、或者前 3 个，也可以全部指定。也就是说，property() 函数中参数的指定并不是完全随意的

  ```python
  class Test:
  
    def __init__(self, n='Tom', age=18, sex='gril'):
      self.__name = n
      self.__age = age
      self.__sex = sex
  
    def getname(self):
      return self.__name
  
    def getage(self):
      return self.__age
  
    def getsex(self):
      return self.__sex
  
    name = property(getname)
    age = property(getage)
    sex = property(getsex)
  
  
  t = Test()
  print(t.name)
  print(t.age)
  print(t.sex)
  ```



# 二、@property装饰器详解

## 2.1 作用

-  @property 装饰器，可以直接通过方法名来访问方法，不需要在方法名后添加一对“（）”小括号

## 2.3 语法和使用

- 如下代码所示

  ```python
  class Test:
  	def __init__(self, n='Tom'):
  		self.__name = n
  
  	@property
  	def getname(self):
  		return self.__name
  
  	name = property(getname)
  
  
  t = Test()
  print(t.getname)
  ```

- 意义：如果是私有变量，设置了@property修饰器后，就可以通过`对象.函数名`来访问修改变量的值





# 七、 type()函数

- `type(obj)`：查看类型 

- `type(name, bases, dict)`:创建类

  - name为类的名称

  - bases为元组，存储该类的父类

  - dict表示一个字典，用于定义类内定义的属性或方法

    ```python
    def func(self):
        print("i am here!!")
    
    # 使用type创建类
    Test = type("Test", (object,), dict(name="Tim", func=func))
    
    #1、元组只有一个元素时，最后的逗号（,）不能省略
    #2、使用 type() 函数创建的类，和直接使用 class 定义的类并无差别。事实上，我们在使用 class 定义类时，Python 解释器底层依然是用 type() 来创建这个类
    
    
    t = Test()
    t.func()
    
    ```

# 八、MetaClass元类

## 8.1 作用

- 概念
  - 元类本质也是一个类
  - 它可以对类内部的定义（包括类属性和类方法）进行动态的修改
- 元类的必须条件
  - 必须显式继承自 type 类
  - 类中需要定义并实现 __new__() 方法，该方法一定要返回该类的一个实例对象，**因为在使用元类创建类时，该 new() 方法会自动被执行，用来修改新建的类**

- 演示代码

  - 在创建类时，通过在标注父类的同时指定元类（格式为`metaclass=元类名`），则当 [Python](http://c.biancheng.net/python/) 解释器在创建这该类时，FirstMetaClass 元类中的 __new__ 方法就会被调用，从而实现动态修改类属性或者类方法的目的
  - cls代表动态修改的类、 name代表动态修改的类名、 bases代表被动态修改的类的所有父类、 attr代表被动态修改的类的所有属性、方法组成的字典

  ```python
  # 定义一个元类，该类添加了一个 name 属性和 say() 方法
  class FirstMetaClass(type):
      def __new__(cls, name, bases, attrs):
          attrs['name'] = "Tim"  # 动态为该类添加一个name属性
          attrs['say'] = lambda self: print("i am here")  # 添加一个方法
          return super().__new__(cls, name, bases, attrs)
        
  # 定义类时指定元类，默认就会添加元类之中的方法
  class Test(metaclass=FirstMetaClass):
      pass
  
  t = Test()
  t.say()
  print(t.name)
  
  ```

## 8.2 底层实现原理

-  python类型模型

  - 所有的 Python 的用户定义类，都是 type 这个类的实例

    ```python
    class MyClass:
        pass
    instance = MyClass()
    print(type(instance))  # <class '__main__.MyClass'>
    print(type(MyClass)  # <class 'type'> 可以看到，这个类的类型为type，即myClass其实是type的对象
    ```

  -  用户自定义类，只不过是 type 类的 __call__ 运算符重载

    ```python
    # 1、定义下面的类
    class MyClass:
      data = 1
      
    # 2、实际执行的是下面这句,等号右边就是type的 __call__运算符重载
    class = type(classname, superclasses, attributedict)
    
    # 3、进一步调用下面的函数
    type.__new__(typeclass, classname, superclasses, attributedict)
    type.__init__(class, classname, superclasses, attributedict)
    ```

  - 一旦把一个类型 MyClass 设置成元类 MyMeta，那么它就不再由原生的 type 创建，而是会调用 MyMeta 的 __call__ 运算符重载

  - **警告：**metaclass 仅仅是给小部分 Python 开发者，在开发框架层面的 Python 库时使用的。而在应用层，metaclass 往往不是很好的选择

# 九、多态

- 满足多态的前提条件

  - 继承：多态一定是发生在子类和父类之间
  - 重写：子类重写了父类的方法

- 代码演示

  ```python
  class Father:
      def func(self):
          print("i am father!!!")
  
  
  class Child(Father):
      def func(self):
          print("i am child")
  
  def test_func(self):
      self.func()
  
  
  #同一个接口，传入不同的对象，调用的函数不一样，这就是多态
  test_func(Father())
  test_func(Child())
  #i am father!!!
  #i am child
  ```

# 十、枚举

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