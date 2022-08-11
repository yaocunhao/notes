# 一、前言

- 对象是由类创建的，先调用`__new__`创建对象，再调用`__init__`进行初始化
- 类默认是有type创建的，类就相当于type的对象，type里面会先调用new创建空对象，再调用inti进行 初始化

# 二、元类

## 2.1 概念悉知

- `___call__`在`对象()`时被调用

- 怎么让一个类的创建改成其它东西呢？元类

  - # 元类，指定类由谁来创建

    ```python
    # type 创建Foo类型
    
    class Foo(object):
      pass
    
    # 东西创建 Foo类型
    class Foo(object,metaclass=东西)：
    	pass
    
    # ----------------------- 
    
      class MyType(type): #继承type
        pass
    
      class Foo(object,metaclass=MyType):
        pass
    
    # Foo类由MyType创建
    
    ```

- Foo由元类创建的，Foo就相当于元类的对象，Foo()就会调用MyType的`__call__()`方法

- 父类由metaclass创建，子类也是由metaclass 创建

- wtfrom源码分析

  ```python
  class FormMeta(type):
    def __init__(cls,name,base,attrs):
      pass
    def __call__(cls,*args,**kwargs):
      return type.__call__(cls,*args,**kwarggs)
  
  def with _metaclass(meta,base=object):
  #				FormMera(base....) 
  #				type(base....)
    return meta()
  
  # 也就是说，FormMeta是From的元类，LoginFrom也由父类的元类创建，也就是说，LoginFrom类的创建会调用FormMeta的new和init
  # 用LoginFrom创建对象的时候，会调用FormMeta的call方法
  
  class From(with_metaclass(FromMeta,BaseFrom){}):
    pass
  
  class LoginFrom(From):
    pass
  
  # 1.创建类时，会执行FormMeta的 __new___ 和 __init__ 
  # 2.根据LpginFrom 创建对象时，会调用FormMeta.__call__方法 -> LoginFrom中的new去创建对象，init去初始化对象
  ```

  

- 作用
  - 类创建时，进行扩展
  - 初始化类时，进行扩展
  - 创建对象时，进行扩展

# 三、单例模式应用

- 代码从上至下，类只创建一次  

