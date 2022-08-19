[参考链接](https://blog.csdn.net/weixin_43901998/article/details/114182863?ops_request_misc=&request_id=&biz_id=102&utm_term=python%20new%20%E6%96%B9%E6%B3%95%E6%8E%A5%E6%94%B6%E5%8F%82%E6%95%B0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-0-114182863.142^v40^pc_rank_34_2,185^v2^control&spm=1018.2226.3001.4187)

# 一 、类和对象的创建

## 1.1 对象的创建过程

- 对象是由类创建的，先调用`__new__`创建**空对象**，再调用`__init__`进行初始化

- 为甚么一定要调用父类的`__new__`去返回实例对象呢？因为`__new__`方法这个申请内存空间并返回的功能一般人不会写，只能用python封装好的，也就是继承object的

- new()方法始终都是类的静态方法，即使没有被加上静态方法装饰器

- **new**()方法构造一个类实例，并将该实例传递给自身的__init__()方法，即__init__()方法的`self`参数

- 如果__new__()方法不返回任何实例的话，**init**()方法将不会被调用

  ```python
  class Base:
  
    def __new__(cls, *args, **kwargs):
      print('new') # 不返回任何实例，init不会调用
  
    def __init__(self, *args, **kwargs):
      print('init')
  
  
  b = Base() # new
  ```

  

- 如果__new__()方法返回一个其他类的实例的话，那它自身的__init__()方法将不会被调用。而且，**new**()方法将会初始化一个其他类的对象

  - 因为实例化的本质就是调用元类的call方法，call中先用new创建一个空对象，然后用这个对象调用init进行初始化。所以，new返回的是谁的对象调用的就是谁的init方法

  ```python
  # new 是静态方法，所以返回谁的实例对象都可以，编译器会根据对象类型，去调用它的init方法进行初始化
  class Foo:
  
    def __init__(self):
      print('Foo init')
  
  
  class Base:
  
    def __new__(cls, *args, **kwargs):
      print('new')
      return Foo()
  
    def __init__(self, *args, **kwargs):
      print('init')
  
  
  b = Base()  # new # Foo init
  ```

  

- 如果重写__new__()方法时，除了`cls`参数外不再设置其他参数的话，将无法用__init__()方法来设置初始化参数

  ```python
  # new 返回一个空的对象，然后编译器根据对象调用它的init函数
  # 如果new没有参数， 那么在调用init的时候，是无法将参数传递过去的
  ```

- 在重写__new__()方法时，需要在参数中加入`*args,**kwargs`，或者显式地加入对应的参数，才能通过__init__()方法初始化参数

  ```python
  class Foo:
  
    def __init__(self):
      print('Foo init')
  
  
  class Base:
    def __new__(cls, *args, **kwargs):
      print('new')
      return super().__new__(cls) # 需要实例化的类名称，然后返回一个空对象
   
    def __init__(self, *args, **kwargs): # 编译器根据空对象执行init函数
      self.name = args[0]
      print('init')
  
  
  b = Base(1)
  print(b.name)
  # new
  # init
  # 1
  ```

- 创建对象和创建类的时候，new的参数不同

  - 创建对象：`return super().__new__(cls)`
  - 创建类：`super().__new__(cls, *args, **kwargs)`


##  1.2 类的创建过程

- 类的本质也是对象，所有的 Python 的用户定义类，都是 type 这个类的实例

- 类默认是由type创建的，类就相当于type的对象，type里面会**先调用new创建空对象(构造方法)，再调用inti进行初始化(初始化方法)**

- 类的init就是给类添加类变量和类方法

  ```python
  # 用户自定义类B1，这个类的实质是type类的对象
  class B1(type):
    pass
  
  # print(type(B1))  # <class 'type'>
  # 可以看到 B1的类型为type，也就是说B1类是type的实例对象，也就是由type创建
  
  
  # 定义一个类
  class B(object):
    def __init__():
      self.name = 'tom'
    
    def __new__(cls,*args,**kwargs):
      data = object.__new__(cls):
      return data
    
  # 创建的过程
  # 1、调用__new__方法创建一个空类，本质就是调用object创建一个空的子类（构造方法）
  # 2、调用__init__ 进行赋值 (初始化方法)
   
  ```

## 1.3 type 类的介绍

- python的内置函数`type()`，其实就是type对象的`__call__`运算符重载(当type()时，就会自动调用`__call__`方法)，**用户自定义类，只不过是 type 类的 __call__ 运算符重载**

- 重载的函数形态：`type(name, bases, dict)`

  - name：当前操作的类名(python之中一切皆对象)
  - base：当前的父类
  - dict：传给当前的属性和方法，这是一个元组，只有一个元素时，最后的`,`不能省略

- 两种使用方法

  - type(name)：查看当前对象的类型

  - type(name, bases,dict) 创建一个名为name的类，它的父类为base，它的属性和方法在dict之中

    - **所以查看一个类的所有成员的方法是：`__dict__`**

      ```python
      # 传统创建，更加的直观
      class Test:
        name='Time'
        def func(self):
          print("i am here!!")
      
          
      # 本质还是使用type的重载创建    
      def func(self):
          print("i am here!!")
      
      # 使用type创建类
      Test = type("Test", (object,), dict(name="Tim", func=func))
      
      #1、元组只有一个元素时，最后的逗号（,）不能省略
      #2、使用 type() 函数创建的类，和直接使用 class 定义的类并无差别。事实上，我们在使用 class 定义类时，Python 解释器底层依然是用 type() 来创建这个类
      
      
      ```

  

# 二、MetaClass 元类

## 2.1 概念介绍

- 概念
  - 元类本质也是一个类
  - 它可以对类内部的定义（包括类属性和类方法）进行动态的修改
- 元类的必须条件
  - 必须**显式继承自 type 类**
  - 类中需要定义并实现 __new__() 方法，该方法一定要返回该类的一个实例对象，**因为在使用元类创建类时，该 new() 方法会自动被执行，用来修改新建的类**

- 类是由type类创建的。指定元类就是，**指定当前类由他的元类创建而不是type类**

  ```python
  # 用户自定义类B1，这个类的实质是type类的对象
  class B1(type):
    pass
  
  # 类B2的本质是，他的元类B1的对象
  # 也就是说B2类是由Base类创建的
  class B2(object,metaclass='B1'):
  	pass
  
  # 可以看到 B1的类型为type， B2的类型为B1
  # 也就是说，B1是由type创建的，B2是由B1创建的
  print(type(B1))  # <class 'type'>
  print(type(B2))  # <class '__main__.B1'>
  ```



## 2.2 元类的使用演示

- 当前类由元类创建，因此调用元类的new、init 方法

  ```python
  class A(type):
  
    def __new__(self, *args, **kwargs):
      # 先创建一个空类
      print('new')
      new_cls = super().__new__(self, *args, **kwargs)
      return new_cls
  
    def __init__(self, *args, **kwargs):
      # 再进行初始化
      print('init')
      super().__init__(*args, **kwargs)
  
  
  class B(object, metaclass=A):
    pass
  
  
  # B是由A创建，所以一定会调用A的new方法创建空对象，然后调用init 方法初始化
  # new
  # init
  ```

  

- B是A类的一个对象，B() 就是调用A的call方法

  ```python
  class A(type):
  
    def __new__(self, *args, **kwargs):
      # 先创建一个空类
      print('new')
      new_cls = super().__new__(self, *args, **kwargs)
      return new_cls
  
    def __init__(self, *args, **kwargs):
      # 再进行初始化
      print('init')
      super().__init__(*args, **kwargs)
  
      
    def __call__(self, *args, **kwargs):
      print('call')
      obj = self.__new__(self) # 调用的B中的new返回对象
      self.__init__(obj, *args, **kwargs) # 调用B中init初始化该对象
      return obj # 返回创建好的对象
  
  
  class B(object, metaclass=A):
     def __init__(self) -> None:
      print('B_init')
  
  B()
  # 先创建元类A的对象B类，调用下面两个方法
  # new
  # init
  
  # 然后B()(对象()),调用的就是call方法，所以会打印call
  # call
  
  # 然后call之中有调用self.init,其实就是B中的init，打印B_init
  # B_init
  
  # 总结：先调用元类的 new 和 init 创建和初始化当前类属性
  # 如果当前类创建对象，相当于调用元类的call，元类的call之中会调用当前类中的new 和 init 初始化一个对象
  ```
  

# 三、应用场景

- 类创建时，进行扩展
- 初始化类时，进行扩展
- 创建对象时，进行扩展

