[参考链接](https://blog.csdn.net/Allenalex/article/details/54097319?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522165569307016782248521539%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fall.%2522%257D&request_id=165569307016782248521539&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~hot_rank-5-54097319-null-null.142^v17^pc_search_result_control_group,157^v15^new_3&utm_term=python+%E6%8F%8F%E8%BF%B0%E7%AC%A6&spm=1018.2226.3001.4187)

# 一、动态增加变量和方法

- Python 可以动态地为类和对象添加类变量

- Python 支持为已创建好的对象**动态增加实例变量**，也就是给对象添加变量。同时也支持使用del进行动态的删除

- Python 也允许为对象动态增加方法。对象动态增加的方法，Python 不会自动将调用者自动绑定到第一个参数（即使将第一个参数命名为 self 也没用）

  - 可以通过MethodType实现不用手动给其传参，即在动态创建的时候就绑定了

  ```python
  # 没有传参
  class Foo:
  	pass
  
  def func():
  	print('hello!!')
  
  f = Foo()
  f.say = func
  f.say() # hello!! 可以看到，func()函数中并没有self参数，如果有需要可以手动传入
  
  # 手动传入
  class Foo:
  	pass
  
  def func(self):
  	print('hello!!',self)
  
  f = Foo()
  f.say = func
  f.say(f) # hello!! <__main__.Foo object at 0x7fd798990240>
  
  # 通过MethodType 在创建的时候就进行绑定
  from types import MethodType
  
  class Foo:
  	pass
  
  def func(self):
  	print('hello!!',self)
  
  f = Foo()
  f.say = MethodType(func,f) # 在创建之时就进行绑定
  f.say() # hello!! <__main__.Foo object at 0x7fd798990240>
  ```

  

# 二、类变量

## 2.1 类变量

- 类变量指的是，在各个类方法外定义的变量
- 类变量的特点是，所有类的实例化对象都同时共享类变量，也就是说，类变量在所有实例化对象中是作为**公用资源**存在的
- 类方法的调用方式有 2 种，**既可以使用类名直接调用，也可以使用类的实例化对象调用**
- **注意**
  - 因为类变量为所有实例化对象共有，通过类名修改类变量的值，**会影响所有的实例化对象**
  - **通过类对象是无法修改类变量的**。通过类对象对类变量赋值，其**本质将不再是修改类变量的值，而是在给该对象定义新的实例变量**，**所以不建议通过对象修改类变量**
  - 类中，实例变量和类变量可以同名，但这种情况下使用类对象将无法调用类变量，它会首选实例变量，这也是不推荐“类变量使用对象名调用”的原因

## 2.2 实例变量

- 实例变量指的是在任意类方法内部，以“self.变量名”的方式定义的变量，其特点是**只作用于调用方法的对象**

- 为什么实例变量变量只属于每个对象？

  - 因为构造函数在初始化对象的时候才会自动调用，此时才会给每个对象创建对象的实例变量

- 可以通过self变量进行赋值，在特定时候调用函数来为其创建实例变量

  ```python
  class Foo:
  	def func(self):
  		self.name = 'zs'
  		self.age =456
  
  f = Foo()
  f.func()
  print(f.name,":",f.age) # zs : 456
  
  ```

## 2.3 局部变量

- **函数之中的变量称之为局部变量**

- 除了实例变量，类方法中还可以定义局部变量。和前者不同，局部变量直接以“变量名=值”的方式进行定义
- 通常情况下，定义局部变量是为了所在类方法功能的实现。需要注意的一点是，局部变量只能用于所在函数中，函数执行完成后，局部变量也会被销毁

# 三、类方法

## 3.1 分类

- 和类属性一样，类方法也可以进行更细致的划分，具体可分为类方法、实例方法和静态方法
- 采用 @classmethod 修饰的方法为类方法；采用 @staticmethod 修饰的方法为静态方法；不用任何修改的方法为实例方法

## 3.2 实例方法

- 通常情况下，在类中定义的方法默认都是实例方法(也就是说没有进行修饰的)

- 实例方法最大的特点就是，**它最少也要包含一个 self 参数**，用于绑定调用此方法的实例对象（Python 会自动完成绑定）

- Python 也支持使用类名调用实例方法，但此方式需要手动给 self 参数传值

  ```python
  Foo.func(Foo()) # 使用类名进行调用，手动传入Foo()对象
  ```

- Python 中允许使用类名直接调用实例方法，但必须手动为该方法的第一个 self 参数传递参数，这种调用方法的方式被称为**“非绑定方法**”

  - 传入的self不一定是当前类实例化出来的对象

  ```python
  class Foo:
  	name = 'zs'
  	
  	def func(self):
  		print(self)
  
  
  Foo.func('123') # 123
  ```

## 3.3 类方法

- Python 类方法和实例方法相似，它最少也要包含一个参数，只不过类方法中通常将其命名为 cls，Python 会自动将类本身绑定给 cls 参数（注意，绑定的不是类对象）

- 和 self 一样，cls 参数的命名也不是规定的（可以随意命名），只是 Python 程序员约定俗称的习惯而已。**即参数的名称并不重要**

  ```python
  
  class Foo:
  	def func(cls): # cls 和 self只是一个名称而已，本质并没有改变
  		print('hello')
  
  Foo.func(Foo())
  ```

- 和实例方法最大的不同在于，类方法需要使用`＠classmethod`修饰符进行修饰

- 类方法推荐使用类名直接调用，当然也可以使用实例对象来调用（不推荐）

## 3.4 静态方法

- 静态方法，其实就是我们学过的函数，和函数唯一的区别是，静态方法定义在类这个空间（类命名空间）中，而函数则定义在程序所在的空间（全局命名空间）中
- 静态方法没有类似 self、cls 这样的特殊参数，因此 Python 解释器不会对它包含的参数做任何类或对象的绑定。也正因为如此，**类的静态方法中无法调用任何类属性和类方法**

- 静态方法需要使用`＠staticmethod`修饰

## 3.5 方法之间的相互调用

- 类中的方法之间相互调用，要带上"前缀"。**因为类相当于一个命名空间**，如果不带上命名空间是无法找到对应的函数的

  ```python
  class Base:
  
    @classmethod
    def func2(cls):
      print('cls')
  
    def func1(self):
      Base.func2() # 这是一个类方法，用类名进行调用
      print('self')
  
  
  b = Base()
  b.func1()
  
  ```

  



# 四、类的命名空间和属性访问顺序

## 4.1 命名空间

- 当定义一个类时，就相当于定义一个命名空间

- 可以通过`__dict__`查看类或者变量的属性

  ```python
  class Base:
    age = 18 
    
    def __init__(self):
      self.name = 'tom'
  
  b = Base()
  
  # 查看类/对象命名空间之中的内容 
  
  print(Base.__dict__) 
  # {'__module__': '__main__', 'age': 18, '__init__': <function Base.__init__ at 0x1063a5550>, '__dict__': <attribute '__dict__' of 'Base' objects>,
  # '__weakref__': <attribute '__weakref__' of 'Base' objects>, '__doc__': None}
  # 没有name属性，因为这个属性是属于对象的
  
  print(b.__dict__) 
  # {'name': 'tom'} 没有age属性，因为这个属性是属于类的，所以通过对象修改类变量，本质是给对象创建一个变量
  
  ```

  

## 4.2 对象属性的访问顺序

- 实例属性
- 类属性
- 父类属性
- getattr() 方法(描述符)



# 五、slots限制类实例动态添加方法

## 5.1 背景介绍

- Python 语言允许为类动态地添加 3 种方法；但对于**实例对象，则只允许动态地添加实例方法**，不能添加类方法和静态方法
- 为单个实例对象添加方法，不会影响该类的其它实例对象；而如果为类动态地添加方法，则所有的实例对象都可以使用

## 5.2 作用

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
