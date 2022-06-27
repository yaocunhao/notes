# 一、是什么

- Python 之中，通过使用描述符，可以让程序员在引用一个对象属性时自定义要完成的工作
- 本质上看，描述符就是一个类，只不过它定义了另一个类中属性的访问方式。换句话说，一个类可以将属性管理全权委托给描述符类
  - 描述符是 Python 中复杂属性访问的基础，它在内部被用于实现 property、方法、类方法、静态方法和 super 类型
- 描述符类基于以下 3 个特殊方法，换句话说，这 3 个方法组成了描述符协议：
  -  **get**(self, instance, owner)
  -  **set**(self, instance, value)
  -  **del**(self, instance)
    - instance是这个描述符属性所在的类的实体对象，而owner是描述符所在的类
  - 其中，实现了 setter 和 getter 方法的描述符类被称为数据描述符；反之，如果只实现了 getter 方法，则称为非数据描述符



# 二、描述符的种类和优先级

- 常情况下，访问一个对象的搜索链是怎样的？比如a.x,首先，应该是查询 a._dict_[‘x’]，然后是type(a)._dict_[‘x’]，一直向上知道元类那层止（不包括元类）。**如果这个属性是一个描述符呢？那python就会“拦截”这个搜索链，取而代之调用描述符方法（_get_）**

- 在Python中访问一个属性的优先级顺序
  - 非数据描述符的访问顺序
    - 类属性 > 数据描述符 > 实例属性 > 非数据描述符 > 找不到的属性触发__getattr__()
  - 数据描述符的访问顺序
    - 数据描述符＞实体属性（存储在实体的_dict_中）>非数据描述符
- 获取一个属性的时候
  - 首先，看这个属性是不是一个数据描述符，如果是，就直接执行描述符的_get_，并返回值
  - 其次，如果这个属性不是数据描述符，那就按常规去从_dict_里面取
  - 最后，如果_dict_里面还没有，但这是一个非数据描述符，则执行非数据描述符的_get_方法，并返回
    

# 三、进一步理解描述符

- 解析描述符方法的参数

  ```python
  #代码 1
  
  class Desc(object):
      
      def __get__(self, instance, owner):
          print("__get__")
          print("self : \t\t", self)
          print("instance : \t", instance)
          print("owner : \t", owner)
          
  
  class TestDesc(object):
      x = Desc()
  
  t = TestDesc()
  t.x
  
  # __get__
  # self :           <__main__.Desc object at 0x7fcd569902b0> Desc实例对象，也就是TestDesc 的属性x
  # instance :       <__main__.TestDesc object at 0x7fcd569902e8>  当前的实例对象，也就是t
  # owner :          <class '__main__.TestDesc'> 当前的拥有者，也就是类 TestDesc
  
  
  ```

  - 当调用`t.x`进行属性访问时，首先会调用Owner的__getattribute__()方法（其实就是 TestDesc.**getattribute**())，访问实例属性，发现没有，然后去访问类属性，找到类属性x,**并且判断属性x为一个描述符**，因此会将TestDesc.x 转化为 TestDesc.**dict**[‘x’].**get**(None, TestDesc) 来访问。然后，进入类Desc的 **get**()方法，进行相应的操作

- 描述符对象不能是实例属性

  ```python
  #代码 1
  
  class Desc(object):
      
      def __get__(self, instance, owner):
          print("__get__")
          print("self : \t\t", self)
          print("instance : \t", instance)
          print("owner : \t", owner)
          
  
  class TestDesc(object):
      def __init__(self):
        self.y = Desc()
      x = Desc()
  
  
  t = TestDesc()
  t.x
  t.y
  
  # __get__
  # self :           <__main__.Desc object at 0x7fcf05190160>
  # instance :       <__main__.TestDesc object at 0x7fcf051902b0>
  # owner :          <class '__main__.TestDesc'>
  ```

  - 可以看到t.y并没有调用`get`方法，这是因为