# 一、装饰器的概念

- 是什么
  - 装饰器本身就是一个函数，一个返回函数的高阶函数

- 作用
  - 在**不修改源代码及调用方式**的前提下，额外的增加新的功能
- 开发封闭原则
  - 对额外新增的功能是开发
  - 对源代码及调用方式是封闭
- 应用场景
  - 面向对象工程
  - 登录认证(装饰器验证成功，再调用被装饰器逻辑)

二、装饰器的由来

- 比如库之中有一个旧的函数，现在又想要对其进行功能的扩充，但是不能改变它自身的代码，以及调用的方式，通常做法如下

  ```python
  import time
  
  def log(func):
    def wrapper(*args, **kwargs):
      print(time.time()) # 在此基础上增加的功能
      func(*args, **kwargs)
  
    return wrapper
  
  
  # 假设这是原来的功能
  def func():
    print("now here")
  
  
  # 由此可见，没有改变原来的代码，也没有改变调用的方式
  func = log(func)
  func()
  
  ```

- 上述这种对原来函数的包装就是装饰器

- 语法糖

  - 很显然，这种调用的方式比较累赘，因此就有了如下所示的语法糖

    ```python
    import time
    
    def log(func):
      def wrapper(*args, **kwargs):
        print(time.time()) # 在此基础上增加的功能
        func(*args, **kwargs)
      return wrapper
    
    
    # 假设这是原来的功能
    @log # -> 相当于执行了 func = log(func)
    def func():
      print("now here")
    
    func()
    ```

# 三、装饰器参数

- 由上可知，其实最后调用的就是wrapper函数，这个函数的参数为动态参数，因此是支持传入参数的

  ```python
  def log(func):
    def wrapper(*args, **kwargs):
      print("start:", args)  # 在此基础上增加的功能
      func(*args, **kwargs)
      print("end:", kwargs)  # 在此基础上增加的功能
    return wrapper
  
  # 假设这是原来的功能
  @log # 其实这是就是 func = log(func)
  def func(*args, **kwargs):
    print("now func")
  
  func(123, a=2, b=3)
  # run result
  # start: (123,)
  # now func
  # end: {'a': 2, 'b': 3}
  ```

- 如果想给装饰器 log 本身增加传入参数，那么需要编写一个**返回装饰器**的高阶函数

  ```python
  def log(arg):
    print(arg)
    def decorator(func):
      def wrapper(*args,**kwargs):
        print("start:",args)
        func(*args,**kwargs)
        print("end:",kwargs)
      return wrapper
    return decorator
  
  
  # 下面这个装饰器的调用过程是
  # 1、先调用log，然后得到@decorator
  # 2、再调用decorator(func)，得到wrapper
  @log("add parse") 
  def func(*args, **kwargs):
    print("now func")
  
  func(123, a=2, b=3)
  # add parse
  # start: (123,)
  # now func
  # end: {'a': 2, 'b': 3}
  ```

  - 语法上只支持**多套**一层，因此需要多层的时候，可以先手动传参，再进行嵌套

  - **在语法上是先触发传参动作，触发装饰器动作**

    ```python
    f = log("parse")# 多套的一层，手动传参得到函数
    @f("parse") # 这里只剩三层，使用语法即可
    ```

# 四、多个装饰器装饰一个函数

- 先执行离被装饰函数最近的语法糖

  ```python
  def wraper(func):
    def innerl(*args, **kwargs):
      print("start")
      func()
      print("end")
    return innerl
  
  
  def wraper2(func):
    def innerl2(*args,**kwargs):
      print("start2")
      func()
      print("end2")
    return innerl2
  
  @wraper
  @wraper2
  def func():
    print("Action")
  
  # func()
  # 执行逻辑
  # 1、func = wraper2(func)
  # 2、func = wraper(func)
  # 3、func() 此时的func 其实是innerl，所以会先打印start
  # 然后执行func()，这个func()是wraper2(func),其实就是innerl2，所以打印start2,然后实际的func() -> Action
  # 然后执行 end2,再执行end
  
  # 小技巧
  # 从上往下执行
  # 先执行wraper，然后wraper2,然后func，然后wraper2，然后wraper
  ```

#  五、一个完整的装饰器

- 函数也是一个对象，函数对象可以被赋值给变量，所以通过变量也能够调用函数

- 函数对象有一个`__name__`属性，可以拿到函数的名字

- 函数经过装饰器后，函数对象的`__name__`就改变了，因此依赖函数签名的代码执行就会错误，**所以需要将原始函数的`__name__`属性拷贝到修饰器函数之中**，通过functools.wraps来完成

  ```python
  import functools
  
  def warper(func):
    def innerl(*args, **kwargs):
      func()
    return innerl
  
  @warper
  def func():
    print("here!!")
  
  print(func.__name__) # innerl
  
  
  # 改变后
  import functools
  
  def warper(func):
    @functools.wraps(func) # 把原始函数的`__name__`等属性复制到innerl函数中
    def innerl(*args, **kwargs):
      func()
    return innerl
  
  @warper
  def func():
    print("here!!")
  
  print(func.__name__) # func
  
  
  ```

  

六、使用技巧

##  6.1 传参技巧

- 多个装饰器装饰同一个函数，并且装饰器拥有多层，那么可以先进行传参

- 这种嵌套使用的情况之下，先执行的是传参动作

  ```python
  def log(arg):
    print(arg)
  
    def wraper(func):
      def innerl(*args, **kwargs):
        print("log start")
        func(*args, **kwargs)
        print("log_end")
      return innerl
    return wraper
  
  
  
  def check(arg):
    print(arg)
  
    def wraper(func):
      def innerl(*args, **kwargs):
        print("check start")
        func(*args, **kwargs)
        print("check end")
      return innerl
    return wraper
  
  
  @log("log")
  @check("check")
  def func():
    print("hello")
  
  
  
  # 呈现出来的调用顺序
  # log
  # check -> 可以看出先执行的是传参动作
  
  # log start
  # check start
  # hello
  # check end
  # log_end
  ```


## 6.2 巧妙运用call 函数

- ``__call__``

  - 如果定义了call函数，对象名()的方式就会调用这个函数，因此可以使用一个类来代替装饰器
  - 类中的``__init__`` 则可以当做参数接收器

- 代码演示

  ```python
  class Test():
    def __init__(self) -> None: # 相当于参数接收器
        self.name = 'str'
    
    def __call__(self,func):  # 直接就进行了函数的调用
        func()
        print(self.name)
  
  
  
  # 此时，Test() 实际上就是 call，如果加上() 就实现了对call函数的调用
  @Test()
  def func():
    print("i am func")
  
  #i am func
  #str
  ```

  