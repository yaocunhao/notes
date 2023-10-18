# 一、前言

- Python 类中，凡是以双下划线 "__" 开头和结尾命名的成员（属性和方法），都被称为类的特殊成员（特殊属性和特殊方法）。例如，类的 __init__(self) 构造方法就是典型的特殊方法
- Python 类中的特殊成员，其特殊性类似 C++ 类的 private 私有成员，即不能在类的外部直接调用，但允许借助类中的普通方法调用甚至修改它们。如果需要，还可以对类的特殊方法进行重写，从而实现一些特殊的功能

# 二、`__new()__`

- [参考链接](https://www.cnblogs.com/34fj/p/6358702.html)

- `__new__()` 是一种**负责创建类实例的静态方法**，它无需使用 staticmethod 装饰器修饰，**且该方法会优先 init() 初始化方法被调用**
- 一般情况下，覆写 `__new()__` 的实现将会使用合适的参数调用其父类 `super().__new__()`，并在返回之前修改实例



# 三、 `__repr()__`

- 作用：显示类的属性

- 原理

  - object之中有__repr()__方法，而我们的类都继承于这个祖先类，因此也都含有这个方法。我们print输出一个类的信息的时候实际就是调用的这个方法

  - 默认情况之下，__repr__() 会返回和调用者有关的 “类名+object at+内存地址”信息

    ```python
    # 默认方法
    # class Test:
    #     pass
    # t = Test()
    # print(t) # <__main__.Test object at 0x7fdcb9a57828>
    
    # 重写repr
    class Test:
        def __repr__(self):
            return "i am here"
    
    
    t = Test()
    print(t)  # i am here
    
    ```

# 四、`__del()__`

- 在进行资源释放的时候会自动调用`__del()__函数。(大多数情况下，Python 开发者不需要手动进行垃圾回收，因为 Python 有自动的垃圾回收机制（下面会讲），能自动将不需要使用的实例对象进行销毁)

- 自动回收的机制采用的是引用技术，和C++之中shared_ptr的引用计数是一样的

- 如果我们重写子类的 __del__() 方法（父类为非 object 的类），则必须显式调用父类的 __del__() 方法，这样才能保证在回收子类对象时，其占用的资源（可能包含继承自父类的部分资源）能被彻底释放

  ```python
  class Father:
      def __del__(self):
          print("father del")
  
  class Child(Father):
      def __del__(slef):
          print("child del")
  
  class Child2(Father):
      pass
  
  c = Child()  # child del -> 没有调用父类的del
  c2 = Child2()  # father del -> 没有重写会调用
  
  ```

# 五、dir()

- 通过此函数可以后去某个对象拥有的所有的属性名和方法名，该函数会返回一个包含有所有属性名和方法名的有序列表
- 通过 dir() 函数，不仅仅输出本类中新添加的属性名和方法，还会输出从父类（这里为 object 类）继承得到的属性名和方法名
- dir() 函数的内部实现，其实是在调用参数对象 `__dir__()` 方法的基础上，对该方法返回的属性名和方法名做了排序

- 代码演示

  ```python
  class Father:
      name = "tim"
      age = 20
  
  
  f = Father()
  print(dir(f))
  print(f.__dir__())
  
  #使用 __dir__() 方法和 dir() 函数输出的数据是相同，仅仅顺序不同
  # ['__class__', '__delattr__', '__dict__', '__dir__', '__doc__', '__eq__', '__format__', '__ge__', .....]
  # ['__module__', 'name', 'age', '__dict__', '__weakref__', '__doc__', '__repr__', '__hash__', '_'.....]
  
  ```

# 六、`__dict__`

- 在python内部，无论是类属性还是实例属性，**都是以字典的形式进行存储的，其中属性名作为键，而值作为该键对应的值**

- 为了方便用户查看类中包含哪些属性，Python 类提供了 __dict__ 属性

  - 该属性可以用类名或者类的实例对象来调用，用类名直接调用 __dict__，会输出该由类中**所有类属性**组成的字典

  - 而使用类的实例对象调用 __dict__，会输出由类中**所有实例属性**组成的字典

    ```python
    class Father:
        def __init__(self, name='jim'):
            self.name = name
        name = "tim"
        age = 20
    
    f = Father()
    # 类名调用
    print(Father.__dict__) #{'__module__': '__main__', 'name': 'tim', 'age': 20,
    
    # 实例调用
    print(f.__dict__) #{'name': 'jim'}
    ```

- 对于具有继承关系的父类和子类来说，父类有自己的 __dict__，同样子类也有自己的 __dict__，它不会包含父类的 __dict__

- 借助**由类实例对象**调用 __dict__ 属性获取的字典，可以使用字典的方式对其中实例属性的值进行修改。**无法通过类似的方式修改类变量的值**

  ```python
  class Father:
      def __init__(self, name='jim'):
          self.name = name
  
      name = "tim"
      age = 20
  
  f = Father()
  f.__dict__['name'] = 'Tom'
  print(f.name) # Tom 
  ```

# 七、hasattr()、getattr()、setattr()函数

## 7.1 hasattr()

- hasattr() 函数用来判断某个类实例对象是否包含指定名称的属性或方法

  - 语法格式：`hasattr(obj, name)`

  - 我们只能通过该函数判断实例对象是否包含该名称的属性或方法，但不能精确判断，该名称代表的是属性还是方法

    ```python
    class Father:
        def __init__(self, name='jim'):
            self.name = name
    
        name = "tim"
        age = 20
    
    f = Father()
    print(hasattr(f, "name")) #True
    
    ```

## 7.2 getattr()

- getattr() 函数获取某个类实例对象中指定属性的值

  - 语法格式：`getattr(obj, name[, default])`

    - obj 表示指定的类实例对象
    - name 表示指定的属性名
    - default 是可选参数，用于设定该函数的默认返回值，即当函数查找失败时，如果不指定 default 参数，则程序将直接报 AttributeError 错误，反之该函数将返回 default 指定的值

    ```python
    class Father:
        def __init__(self, name='jim'):
            self.name = name
    
        @classmethod
        def func(cls):
            pass
    
        name = "tim"
        age = 20
    
    f = Father()
    print(getattr(f, "name"))  # jim
    print(getattr(f, "func"))  # <bound method Father.func of <class '__main__.Father'>>
    print(getattr(f, "nameccc", 12345)) # 12345 -> 找不到时返回自己设置的值
    ```

## 7.3 setattr()

- 语法格式：`setattr(obj, name, value)`

- 修改类实例对象中的属性值、还可以实例对象动态添加属性或者方法

- 使用 setattr() 函数对实例对象中执行名称的属性或方法进行修改时，如果该名称查找失败，Python 解释器不会报错，而是会给该实例对象动态添加一个指定名称的属性或方法

  ```python
  class Father:
      def __init__(self, name='jim'):
          self.name = name
  
      @classmethod
      def func(cls):
          print("i am here!!")
  
      age = 20
  
  
  f = Father()
  setattr(f, "age", 100)  # 修改某个类对象的属性值
  print(f.age)  # 100
  
  
  def add():
      print("computer")
  
  45
  value = "i am here"
  
  setattr(f, "name", add)  # 将属性修改为方法
  setattr(f, "func", value)  # 将方法修改为属性
  f.name()  # computer
  print(f.func)  # i am here
  
  #修改时找不到 -> 会自动添加
  setattr(f, "value2", value)
  setattr(f, "test", add)
  print(f.value2) # i am here
  f.test() # computer
  ```

# 八、 issubclass和isinstance函数

- 作用：类型检查

  -  **issubclass检查前面是否是后面的子类，isinstance前面是否是后面的实例化对象**

  - issubclass(cls, class_or_tuple)：检查 cls 是否为后一个类或元组包含的多个类中任意类的**子类**

  - isinstance(obj, class_or_tuple)：检查 obj 是否为后一个类或元组包含的多个类中任意类的**对象**

  - 通过使用上面两个函数，程序可以方便地先执行检查，然后才调用方法，这样可以保证程序不会出现意外情况

    ```python
    # 定义一个字符串
    hello = "Hello";
    print('"Hello"是否是str类的实例: ', isinstance(hello, str))  # T
    print('"Hello"是否是object类的实例: ', isinstance(hello, object))  # T
    print('str是否是object类的子类: ', issubclass(str, object))  # T
    print('"Hello"是否是tuple类的实例: ', isinstance(hello, tuple))  # F
    print('str是否是tuple类的子类: ', issubclass(str, tuple))  # F
    
    # 定义一个列表
    my_list = [2, 4]
    print('[2, 4]是否是list类的实例: ', isinstance(my_list, list))  # T
    print('[2, 4]是否是object类及其子类的实例: ', isinstance(my_list, object))  # T
    print('list是否是object类的子类: ', issubclass(list, object))  # T
    print('[2, 4]是否是tuple类及其子类的实例: ', isinstance([2, 4], tuple))  # f
    print('list是否是tuple类的子类: ', issubclass(list, tuple))  # F
    
    ```

# 九、 `__bases__` 属性、`__subclasses__`方法

- Python 为所有类都提供了一个 __bases__ 属性，通过该属性可以查看该类的所有直接父类，该属性返回所有直接父类组成的元组

- Python 还为所有类都提供了一个 __subclasses__() 方法，通过该方法可以查看该类的所有直接子类，该方法返回该类的所有子类组成的列表

  ```python
  class A:
      pass
  
  
  class B(A):
      pass
  
  
  print(B.__bases__)
  print(B.__subclasses__())
  print(A.__bases__)
  print(A.__subclasses__())
  # (<class '__main__.A'>,)
  # []
  # (<class 'object'>,)
  # [<class '__main__.B'>]
  ```

  

# 十、`__Call()__`方法

## 10.1 使用方法

- 该方法的功能类似于在类中重载 () 运算符，使得类实例对象可以像调用普通函数那样，以“对象名()”的形式使用

  - Python 中，凡是可以将 () 直接应用到自身并执行，都称为可调用对象。可调用对象包括自定义的函数、Python 内置函数以及此处所讲的类实例对象
  - 对于可调用对象，实际上“名称()”可以理解为是“名称.__call__()”的简写

  ```python
  class A:
      def __call__(self, name, func):
          print("调用__call()__方法", name, func)
  
  
  def func():
      print("i am here!!")
  
  
  a = A()
  a("Tom", func)  # 调用__call()__方法 Tom <function func at 0x7f8bb5fda048>
  a.__call__("Tom", func) # 调用__call()__方法 Tom <function func at 0x7fbb1a5da048>
  
  
  def say():
      print("test")
  
  say() #test
  say.__call__() #test -> 说明了自定义函数也是可调用对象
  ```

## 10.2 用 `__call()__` 弥补 hasattr() 函数的短板

- hasattr无法判断找到的是属性还是方法，类之中的可调用方法也属于可调用对象，类属性不是，因此可以使用call进行改进

  ```python
  class A:
      def __init__(self):
          self.name = "Jim"
  
      def func():
          pass
  
  
  #判断name、func是变量还是方法
  a = A()
  if hasattr(a, "name"):
      print(hasattr(a.name, "__call__"))  # False 判断name之中是否有__call__
  
  if hasattr(a, "func"):
      print(hasattr(a.func, "__call__")) # True
  
  ```

# 十一、运算符重载

| 重载运算符                                     | 含义                                                         |
| ---------------------------------------------- | ------------------------------------------------------------ |
| __new__                                        | 创建类，在 __init__ 之前创建对象                             |
| __init__                                       | 类的构造函数，其功能是创建类对象时做初始化工作。             |
| __del__                                        | 析构函数，其功能是销毁对象时进行回收资源的操作               |
| __add__                                        | 加法运算符 +，当类对象 X 做例如 X+Y 或者 X+=Y 等操作，内部会调用此方法。但如果类中对 __iadd__ 方法进行了重载，则类对象 X 在做 X+=Y 类似操作时，会优先选择调用 __iadd__ 方法。 |
| __radd__                                       | 当类对象 X 做类似 Y+X 的运算时，会调用此方法。               |
| __iadd__                                       | 重载 += 运算符，也就是说，当类对象 X 做类似 X+=Y 的操作时，会调用此方法。 |
| __or__                                         | “或”运算符 \|，如果没有重载 __ior__，则在类似 X\|Y、X\|=Y 这样的语句中，“或”符号生效 |
| __repr__，__str__                              | 格式转换方法，分别对应函数 repr(X)、str(X)                   |
| __call__                                       | 函数调用，类似于 X(*args, **kwargs) 语句                     |
| __getattr__                                    | 点号运算，用来获取类属性                                     |
| __setattr__                                    | 属性赋值语句，类似于 X.any=value                             |
| __delattr__                                    | 删除属性，类似于 del X.any                                   |
| __getattribute__                               | 获取属性，类似于 X.any                                       |
| __getitem__                                    | 索引运算，类似于 X[key]，X[i:j]                              |
| __setitem__                                    | 索引赋值语句，类似于 X[key], X[i:j]=sequence                 |
| __delitem__                                    | 索引和分片删除                                               |
| __get__, __set__, __delete__                   | 描述符属性，类似于 X.attr，X.attr=value，del X.attr          |
| __len__                                        | 计算长度，类似于 len(X)                                      |
| __lt__，__gt__，__le__，__ge__，__eq__，__ne__ | 比较，分别对应于 <、>、<=、>=、=、!= 运算符。                |
| __iter__，__next__                             | 迭代环境下，生成迭代器与取下一条，类似于 I=iter(X) 和 next() |
| __contains__                                   | 成员关系测试，类似于 item in X                               |
| __index__                                      | 整数值，类似于 hex(X)，bin(X)，oct(X)                        |
| __enter__，__exit__                            | 在对类对象执行类似 with obj as var 的操作之前，会先调用 __enter__ 方法，其结果会传给 var；在最终结束该操作之前，会调用 __exit__ 方法（常用于做一些清理、扫尾的工作） |

# 十二、迭代器

- 概念

  - 迭代器指的就是支持迭代的容器，更确切的说，是支持迭代的容器类对象，这里的容器可以是列表、元组等这些python提供的基础容器，也可以是自定义的容器类对象，只要该容器支持迭代即可

- 实现自定义迭代器，类中必须实现以下两种方法

  - `__next__(self)`：返回容器的下一个元素

  - `__iter__(self)`：该方法返回一个迭代器（iterator）

    ```python
    # 简单的支持迭代的容器
    class Demo:
        def __init__(self):
            self.__data = []
            self.__sub = 0
    
        def __next__(self):
            if self.__sub <= 0:
                raise StopIteration
            self.__sub -= 1
            return self.__data[self.__sub]
    
        def __iter__(self):
            return self  # 实例对象就是迭代器本身
    
        #设置参数，重载
        def __setitem__(self, key, value):
            self.__data.insert(key, value)
            self.__sub += 1
    
    
    t = Demo()
    t[0] = 0
    t[1] = 1
    for v in t:
        print(v #1 0
    
    ```

- Python 内置的 iter() 函数也会返回一个迭代器，该函数的语法格式如下

  - `iter(obj[, sentinel])`

    - obj 必须是一个可迭代的容器对象

      ```python
      # 将列表转换为迭代器
      myIter = iter([1, 2, 3])
      
      # 依次获取迭代器的下一个元素，也可以使用内置函数 next()来迭代，即 next(myIter)
      print(myIter.__next__())
      print(myIter.__next__())
      print(myIter.__next__())
      # print(myIter.__next__()) 越界抛异常
      ```

      

    - 而 sentinel 作为可选参数，如果使用此参数，要求 obj 必须是一个可调用对象。这样当使用返回的迭代器用`__next()__`方法时，它会通过执行obj()调用`__call()__`方法，如果该方法的返回值和第二个参数相同，则输出异常，否则输出`__call（）__`方法的返回值

      ```python
      # 简单的支持迭代的容器
      class Demo:
          def __init__(self):
              self.__data = []
              self.__sub = 0
      
          # 设置参数，重载
          def __setitem__(self, key, value):
              self.__data.insert(key, value)
              self.__sub += 1
      
          def __call__(self):
              self.__sub -= 1
              return self.__data[self.__sub]
      
      
      t = Demo()
      t[0] = 1
      t[1] = 133
      
      it = iter(t, 1)  # 获取迭代器
      print(it.__next__()) 
      print(it.__next__())
      #133 StopIteration，这还是因为第二次输出的1和传入iter的第二个参数相等
      ```

# 十三、生成器

- 概念

  - 生成器本质上也是迭代器，不过它比较特殊
  - 以 list 容器为例，在使用该容器迭代一组数据时，必须事先将所有数据存储到容器中，才能开始迭代；**而生成器却不同，它可以实现在迭代的同时生成元素**
  - 也就是说，对于可以用某种算法推算得到的多个数据，生成器并不会一次性生成它们，而是**什么时候需要，才什么时候生成**

- 生成器的创建方式

  - 定义一个以 yield 关键字标识返回值的函数

  - 调用刚刚创建的函数，即可创建一个生成器

  - 如下述代码所示，成功创建了一个 num 生成器对象。显然，和普通函数不同，intNum() 函数的返回值用的是 yield 关键字，而不是 return 关键字，此类函数又成为生成器函数

    ```python
    def intNum():
        print("开始执行")
        for i in range(5):
            yield i				#执行完之后就会在此暂停
            print("继续执行")
    
    num = intNum() # 得到生成器
    ```

  - 和 return 相比，yield 除了可以返回相应的值，还有一个更重要的功能，即每当程序执行完该语句时，程序就会暂停执行。不仅如此，即便调用生成器函数，python解释器也不会执行函数中的代码，它只会返回一个生成器（对象）

- 生成器的执行方式

  - 要想使生成器函数得以执行，或者想使执行完 yield 语句立即暂停的程序得以继续执行，有以下 2 种方式

    - 通过生成器（上面程序中的 num）调用 next() 内置函数或者` __next__() `方法；

    - 通过 for 循环遍历生成器

      ```python
      def intNum():
          print("开始执行")
          for i in range(5):
              yield i
              print("继续执行")
      
      
      num = intNum()  # 得到生成器(是一个对象)
      
      # 调用 next() 内置函数
      print(next(num))
      # 调用 __next__() 方法
      print(num.__next__())
      # 通过for循环遍历生成器
      for i in num:
          print(i)
      
      #result:
      开始执行
      0
      继续执行
      1
      继续执行
      2
      继续执行
      3
      继续执行
      4
      继续执行
      
      ```

      - 使用 list() 函数和 tuple() 函数，直接将生成器能生成的所有值存储成列表或者元组的形式

        ```python
        num=intNum()
        print(list(num))
        
        num=intNum()
        print(tuple(num))
        # 继续执行
        # 开始执行
        # 继续执行
        # 继续执行
        # 继续执行
        # 继续执行
        # 继续执行
        
        
        # [0, 1, 2, 3, 4]
        # 开始执行
        # 继续执行
        # 继续执行
        # 继续执行
        # 继续执行
        # 继续执行
        # (0, 1, 2, 3, 4)
        ```

# 十四、Python生成器（send，close，throw）方法详解

## 14.1 send

- 通过调用 next() 或者 __next__() 方法，可以实现从外界控制生成器的执行。除此之外，通过 send() 方法，**还可以向生成器中传值**

- send() 方法可带一个参数，也可以不带任何参数（用 None 表示）。其中，当使用不带参数的 send() 方法时，它和 next() 函数的功能完全相同

- 该函数还会将 value 值作为 yield 语句返回值赋值给接收者

- 带参数的 send(value) 无法启动执行生成器函数。也就是说，程序中第一次使用生成器调用 next() 或者 send() 函数时，不能使用带参数的 send() 函数

  ```python
  def func():
      v1 = yield "hello"
      v2 = yield v1
      yield v2
  
  
  f = func()
  print(f.send(None))
  print(f.send("i am here !!")
  print(f.send("hahahaha!!"))
  
  # hello
  # i am here !!
  # hahahaha!!
  
  ```

  

## 14.2 Python生成器close()方法

- 当程序在生成器函数中遇到 yield 语句暂停运行时，此时如果调用 close() 方法，会阻止生成器函数继续执行，该函数会在程序停止运行的位置抛出 GeneratorExit 异常

  ```python
  def func():
      try:
          yield 1
      except GeneratorExit:
          print("捕获了GeneratorExit")
  
  
  
  f = func()
  print(next(f))
  f.close()
  
  # 1
  # 捕获了GeneratorExit
  
  ```

- 虽然通过捕获 GeneratorExit 异常，可以继续执行生成器函数中剩余的代码，带这部分代码中不能再包含 yield 语句，否则程序会抛出 RuntimeError 异常

  ```python
  def func():
      try:
          yield 1
      except GeneratorExit:
          print("捕获了GeneratorExit")
  
      yield 2 #忽视异常
  
  f = func()
  print(next(f))
  f.close()
  
  # 1
  # 捕获了GeneratorExit
  # RuntimeError: generator ignored GeneratorExit
  
  ```

- 生成器函数一旦使用 close() 函数停止运行，后续将无法再调用 next() 函数或者 __next__() 方法启动执行，否则会抛出 StopIteration 异常

  ```python
  def func():
      try:
          yield 1
      except GeneratorExit:
          print("捕获了GeneratorExit")
  
  f = func()
  print(next(f))
  f.close()
  print(f.__next__())
  # 1
  # 捕获了GeneratorExit
  # StopIteration
  
  #-----正常运行----
  def func():
      try:
          yield 1
      except GeneratorExit:
          print("捕获了GeneratorExit")
          
      print("not yield continue!!")
  
  f = func()
  print(next(f))
  f.close()
  # 1
  # 捕获了GeneratorExit
  # not yield continue!!
  ```

## 14.3 Python生成器throw()方法

- 生成器 throw() 方法的功能是，在生成器函数执行暂停处，抛出一个指定的异常，之后程序会继续执行生成器函数中后续的代码，直到遇到下一个 yield 语句。需要注意的是，如果到剩余代码执行完毕没有遇到下一个 yield 语句，则程序会抛出 StopIteration 异常

  ```python
  def func():
      try:
          yield 1
      except ValueError:
          print("捕获了Error")
  
      print("continue run")
  
  f = func()
  print(next(f))
  f.throw(ValueError)
  # 1
  # 捕获了Error
  # continue run -> 抛出异常之后，继续执行后面的代码
  # StopIteration -> 没有yiled代码
  ```

# 十五、 @函数装饰器及用法 

## 15.1 常见函数修饰器

- ＠staticmethod、＠classmethod 和 @property三个都是python的内置函数

## 15.2 原理

- 实际上，函数修饰器就是在不改变原函数的前提之下，对函数进行合理的扩充

- 使用函数装饰器 A() 去装饰另一个函数 B()，其底层执行了如下 2 步操作

  - 将 B 作为参数传给 A() 函数

  - 将 A() 函数执行完成的返回值反馈回 B

    ```python
    #funA 作为装饰器函数
    def funA(fn):
        #...
        fn() # 执行传入的fn参数
        #...
        return '...'
    @funA
    def funB():
        #...
        
        
    # 实际上，上面程序完全等价于下面的程序：    
        
      def funA(fn):
        #...
        fn() # 执行传入的fn参数
        #...
        return '...'
    def funB():
        #...
    funB = funA(funB)
    
    # -----实例——————————————————————
    def funcA(fn):
        print("start funcA")
        fn()  # 执行传入的函数
        print("end funcA")
        return "装饰器函数的返回值" #返回值就是funcB
    
    
    @funcA
    def funcB():
        print("start funcB")
    
    print(funcB)
    
    # start funcA
    # start funcB
    # end funcA
    # 装饰器函数的返回值
    
    ```

- 显然，被“＠函数”修饰的函数不再是原来的函数，而是被替换成一个新的东西（取决于装饰器的返回值），即如果装饰器函数的返回值为普通变量，那么被修饰的函数名就变成了变量名；同样，如果装饰器返回的是一个函数的名称，那么被修饰的函数名依然表示一个函数

## 1.5.3 带参数的函数修饰器

- 上述分析之中，即当 funB() 函数无参数时，可以直接将 funB 作为 funA() 的参数传入。但是，如果被修饰的函数本身带有参数，那应该如何传值呢？

  ```python
  def funcA(fn):
      # 定义一个嵌套的函数
      def say(arc):
          print("funcA start", arc)
  
      return say
  
  # 修饰之后，funcB就是funcA的返回值
  @funcA
  def funcB(string="!!!!!!!"):
      print("start funcB", string) #被修饰后 funcB就是say，所以不会执行这一句
  
  funcB("!!!@!@") #funcA start !!!@!@
  ```

## 1.5.4 函数修饰器可以嵌套

- 多个修饰器嵌套的执行顺序

  ```python
  @funA
  @funB
  @funC
  def fun():
      #...
  ```

  - 上面的代码等价于`fun = funA( funB ( funC (fun) ) )` -> 先执行funC，然后将执行的结果塞入到FuncB之中进行执行，再将其塞入到funcA之中

# 十六、super

- [调用父类的方法](https://www.ycpai.cn/python/NRsrheW8.html)
