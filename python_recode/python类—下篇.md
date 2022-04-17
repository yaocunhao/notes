# 一、property() 函数

## 1.1 作用

- 在不破坏类的封装性的基础之上,继续使用`类名.属性名`的放方式进行成员的访问

## 1.2 语法和使用

- `属性名=property(fget=None, fset=None, fdel=None, doc=None)`

  - fget 参数用于指定获取该属性值的类方法
  - fset 参数用于指定设置该属性值的方法
  - fdel 参数用于指定删除该属性值的方法
  - 最后的 doc 是一个文档字符串，用于说明此函数的作用

- 在使用 property() 函数时，以上 4 个参数可以仅指定第 1 个、或者前 2 个、或者前 3 个，当前也可以全部指定。也就是说，property() 函数中参数的指定并不是完全随意的

  ```python
  class Test:
  	def __init__(self, n='Tom'):
  		self.__name = n
  
  	def getname(self):
  		return self.__name
  
  	name = property(getname)
  
  
  t = Test()
  print(t.name)
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



# 三、python封装性的底层实现

- Python 类中所有的属性和方法，都是公有（public）属性，如果希望 Python 底层修改类属性或者类方法的名称，以此将它们隐藏起来，只需将它们的名称前添加双下划线（“__”）即可

- python封装的底层实现其实是将其名字进行更改，导致找不到成员，所以实际还是能够访问到的

- 对于以双下划线开头命名的类属性或类方法，Python 在底层实现时，将它们的名称都偷偷改成了 "_类名__属性（方法）名" 的格式

  ```python
  class Test:
      def __init__(self, n='Tom'):
          self.__name = n
  
  
  t = Test()
  print(t._Test__name) #Tom 访问了变量
  print(t.__name) #不可访问
  
  ```

  

# 四、继承

- 多继承冗余问题：根据子类继承多个父类时这些父类的前后次序决定，**即排在前面父类中的类方法会覆盖排在后面父类中的同名类方法**

- 重写调用父类成员问题：直接使用父类的名称进行调用(**python就是一个命名空间**)，但是需要手动的传入self,称之为未绑定方法

  ```python
  class People:
      def __init__(self, name='Jim'):
          self.__name = name
  
      def getname(self):
          print(self.__name)
  
  
  class Child(People):
      def getname(self):
          print("i not name")
  
  
  c = Child()
  c.getname()  # i not name
  People.getname(c)  # Jim,非绑定用法
  
  ```



# 五、super()函数

## 5.1 使用场景

- 当进行多继承的时候，**python默认只会调用第一个继承的父类的构造函数**，因此需要子类手动调用父类的构造函数，有如下两种调用的方式

  ```python
  class Beef:
      def __init__(self, pe):
          self.price = pe
  
      def get_prive(self):
          print(self.price)
  
  
  class Fruit:
      def __init__(self, num):
          self.number = num
  
      def get_num(self):
          print(self.numbers)
  
  
  class Food(Beef, Fruit):
      pass
  
  
  f = Food(312, 542354 #不接受两个参数
  f.get_prive()
  f.get_num()
  
  ```

  

  - 类可以看做一个独立空间，在类的外部调用其中的实例方法，可以向调用普通函数那样，只不过需要额外备注类名（此方式又称为未绑定方法）
  - 使用 super() 函数
    - 但如果涉及多继承，该函数只能调用第一个直接父类的构造方法，**也就是说涉及到多继承时，在子类构造函数中，调用第一个父类构造方法的方式有以上 2 种，而调用其它父类构造方法的方式只能使用未绑定方法**

## 5.2 使用实例

- 语法`super().__init__(self,...)`

  ```python
  class Beef:
      def __init__(self, pe):
          self.price = pe
  
      def get_prive(self):
          print(self.price)
  
  
  class Fruit:
      def __init__(self, num):
          self.number = num
  
      def get_num(self):
          print(self.number)
  
  
  class Food(Beef, Fruit):
      def __init__(self, price, num):
          super().__init__(price)  # 调用Beef的构造函数可以使用两种方式
          Fruit.__init__(self, num)  # 调用Fruit的构造函数只可以使用一种方式
  
  
  f = Food(312, 542354)
  f.get_prive()  # 312
  f.get_num()  # 542354
  
  ```

## 5.3 注意事项

- 不能在就旧式类中进行调用

  - python3x 往后都是新式类，如果没有写明继承某个类，都默认继承了object这个类

- 混用super与显式类调用，如下代码会导致B类被初始化两次

  ```python
  class A:
      def __init__(self):
          print("A",end=" ")
          super().__init__() #会初始化B，导致B多初始化一次
  class B:
      def __init__(self):
          print("B",end=" ")
          super().__init__()
  class C(A,B):
      def __init__(self): 
          print("C",end=" ")
          A.__init__(self)
          B.__init__(self)
          
  C()
  
  # 正确用法
  class C(A, B):
      def __init__(self):
          print("C", end=" ")
          super().__init__() #调用，初始化两个类
  
  ```

- 不同种类的参数

  - 如果子类和基类的构造函数的参数不同，调用基类的时候就会出现问题

    ```python
    class A:
        def __init__(self):
            print("A", end=" ")
            super().__init__()
    
    
    class B(A):
        def __init__(self):
            print("B", end=" ")
            super().__init__()
    
    
    class C(A):
        def __init__(self):
            print("C", end=" ")
            super().__init__()
    
    
    class D(B, C):
        def __init__(self, arg):
            print("A", end=" ")
            super().__init__(arg)
    
    
    D(123)
    
    # 解决办法使用 *args 和 **kwargs 包装的参数和关键字参数
    # 但是这种方法会导致构造函数可以接收任意类型的参数，代码健壮性不高
    class A:
        def __init__(self, *args, **kwargs):
            print("A", end=" ")
            super().__init__()
    
    
    class B(A):
        def __init__(self, *args, **kwargs):
            print("B", end=" ")
            super().__init__()
    
    
    class C(A):
        def __init__(self, *args, **kwargs):
            print("C", end=" ")
            super().__init__()
    
    
    class D(B, C):
        def __init__(self, arg):
            print("A", end=" ")
            super().__init__(arg)
    
    # A B C A
    ```

- 小结

  - 尽可能避免使用多继承，可以使用一些[设计模式](http://c.biancheng.net/design_pattern/)来替代它；

  - super 的使用必须一致，即在类的层次结构中，要么全部使用 super，要么全不用。**混用 super 和传统调用是一种混乱的写法**

  - 如果代码需要兼容 Python 2.x，在 Python 3.x 中应该显式地继承自 object。在 Python 2.x 中，没有指定任何祖先地类都被认定为旧式类

  - 调用父类时应提前查看类的层次结构，也就是使用类的 __mro__ 属性或者 mro() 方法查看有关类的 MRO

    ```\
    print(D.__mro__)
    #(<class '__main__.D'>, <class '__main__.B'>, <class '__main__.C'>, <class '__main__.A'>, <class 'object'>)
    ```

# 六、__slots__

## 6.1 背景介绍

- Python 语言允许为类动态地添加这 3 种方法；但对于**实例对象，则只允许动态地添加实例方法**，不能添加类方法和静态方法
- 为单个实例对象添加方法，不会影响该类的其它实例对象；而如果为类动态地添加方法，则所有的实例对象都可以使用

## 6.2 作用

- 动态给类或者实例对象添加属性或方法，是非常灵活的。但与此同时，如果胡乱地使用，也会给程序带来一定的隐患，即程序中已经定义好的类，如果不做任何限制，是可以做动态的修改的

- __slots__ **只能**限制**为实例对象动态添加属性和方法**，而无法限制动态地为类添加属性和方法

- __slots__ 属性值其实就是一个元组，只有其中指定的元素，才可以作为动态添加的属性或者方法的名称

  - ` __slots__ = ('name1','name2','name2')`

- 如果为子类也设置有 __slots__ 属性，那么子类实例对象允许动态添加的属性和方法，是子类中 __slots__ 属性和父类 __slots__ 属性的和

  ```python
  class A:
      __slots__ = ('name', 'age')
      pass
  
  a = A()
  a.name = 'jim'
  a.age = 100
  A.weight = "100kg"
  
  print(A.weight)  # 100kg -> slots不能限制为类添加
  a.weight = "60kg"  # error: 'A' object has no attribute 'weight'
  
  ```

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