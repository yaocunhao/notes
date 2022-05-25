- ## isinstance() 函数
  
  - 判断一个对象是否是一个已知的类型，类似 type()
  - 语法：`isinstance(object, classinfo)`
    - object -- 实例对象
    - classinfo -- 可以是直接或间接类名、基本类型或者由它们组成的元组
  
- 调用函数时
  - 不带括号，调用的是函数本身，是整个函数，是一个函数对象，不等该函数执行完成
  - 带括号，调用的是函数的执行结果，需要等待函数执行完成

- hasattr()

  - 函数用于判断对象是否包含对应的属性

    ```python
    hasattr(object, name)
    ```

    - object -- 对象】
    - name -- 字符串，属性名

- if not
  - 和if的语义相反
  - 在使用if not 的时候，如果表达式的结果为false则执行后面的逻辑，如果表达式的结果为true则跳过

- [Python中*args和**kwargs的区别](https://www.cnblogs.com/yunguoxiaoqiao/p/7626992.html)

  -    *args 用来将参数打包成tuple(元组)给函数体调用

  - **kwargs 打包关键字参数成dict给函数体调用

    ```python
    def func(argc, *args, **kwargs):
        print(argc)
        print(args, type(args))
        print(kwargs, type(kwargs))
    
    
    func(1, 2, 3, 4, 5, 6, value=8)
    
    #result
    1
    (2, 3, 4, 5, 6) <class 'tuple'>
    {'value': 8} <class 'dict'>
    ```

- **getattr()** 函数用于一个对象属性值 [参考链接](https://www.runoob.com/python/python-func-getattr.html)

  - ```python
    getattr(object, name, default) # 获取object之中名称为name的属性值
    ```

    - object -- 对象。
    - name -- 字符串，对象属性。
    - default -- 默认返回值，如果不提供该参数，在没有对应属性时，将触发 AttributeError。

- `__getattr()__`

  - 当访问一个对象不存在的属性之时，会抛出异常，这个异常就是由该接口抛出的

    ```python
    class Base:
    	def __getattr__(self,name): #
    		print("not find!!!")
    
    b = Base()
    b.cc   # not find!!!
    
    ```

    

- setattr()

  - 用于设置属性值，该属性不一定是存在的
    - 属性存在则赋值，属性不存在则创建一个属性

  ```python
  setattr(object, name, value)
  ```

  - object -- 对象。
  - name -- 字符串，对象属性。
  - value -- 属性值。

- dict.get()

  - ```python
    dict.get(key, default) 
    ```

    - key -- 字典中要查找的键。
    - default-- 可选，如果指定键的值不存在时，返回该默认值。

- startswith()

  - 检查字符串是否是以指定子字符串开头，如果是则返回 True，否则返回 False。如果参数 beg 和 end 指定值，则在指定范围内检查

    ```python
    str.startswith(str, beg=0,end=len(string));
    ```

    - str -- 检测的字符串。
    - strbeg -- 可选参数用于设置字符串检测的起始位置。
    - strend -- 可选参数用于设置字符串检测的结束位置。

- decode() 方法

  - 以 *encoding* 指定的编码格式解码字符串。默认编码为字符串编码

  - ```python
    str.decode(encoding='UTF-8',errors='strict')
    ```

    - encoding -- 要使用的编码，如"UTF-8"
    - errors -- 设置不同错误的处理方案。默认为 'strict',意为编码错误引起一个UnicodeError。 其他可能得值有 'ignore', 'replace', 'xmlcharrefreplace', 'backslashreplace' 以及通过 codecs.register_error() 注册的任何值
    - **该方法返回解码后的字符串**		

- split() 方法语法：

  - Python **split()** 通过指定分隔符对字符串进行切片，如果参数 num 有指定值，则分隔 num+1 个子字符串

  ```python
  str.split(str="", num=string.count(str)).
  ```

  - str -- 分隔符，默认为所有的空字符，包括空格、换行(\n)、制表符(\t)等。
  - num -- 分割次数。默认为 -1, 即分隔所有。

- python3 新语法：函数注释

  ```python
      def __getattribute__(self, name: str) -> Any: ...
      
      name:str 标识name参数是一个str
      -> 和 ： 之间表示返回值的注释
      ... 标识省略号，表示函数的定义内容不写了
  ```

  

- 列表字典生成式

  - [参考链接](https://www.cnblogs.com/dbf-/p/11888917.html)

  ```python
  # 常规写法
  l = list()
  for i in range(10):
      l.append(i)
  print(l)
  
  # 列表生成式
  l2 = [i**2 for i in range(10)]
  print(l2)
  ```

  

- importlib.import_module()使用方法
  - 动态地获取另一个py文件中定义好的变量/方法，只要设置好py的文件路径即可

    ```python
    from importlib import import_module
    
    def test():
      path = 'test.test' # 模块的路径
      obj = import_module(path) # 导入模块，obj就是test模块
      
      b = obj.Base() # 获取模块之中的类 && 定义出来一个对象
      print(b.name,b.sex,b.age) 
    
      f = obj.func # 获取模块之中的函数
      f()
      print(obj)
    
    test()
    ```

    

- 参数带*号
  - 单星号：*agrs，将所有参数以元组(tuple)的形式导入

  - 双星号：**kwargs，将参数以字典(dict)的形式导入

    ```python
    def func(*args, **kwargs):
        print(type(args), type(kwargs))
        print(args, kwargs)
    
    
    di = {'1': 1, '2': 2}
    l = [1, 2, 3, 4, 5, 65]
    t = (2, 2, 2, 2,)
    
    # 不管传入什么类型的参数，都会编程元组args的一个参数
    # 传入参数的格式为 xx=xx，就会自动变成字典dict的一个键值对
    
    func(di, l, t, a=22, c='3423')
    # <class 'tuple'> <class 'dict'>
    # ({'1': 1, '2': 2}, [1, 2, 3, 4, 5, 65], (2, 2, 2, 2)) {'a': 22, 'c': '3423'}
    ```

    

- from threading import local
  - 为每个线程创建一个空间(本地线程存储技术)

- import_modul

  - ```
    from importlib import import_module
    t = import_module(包的名字)
    ```

  - 返回值为这个包(文件)的对象，t.name 从这个文件中获取变量，类等等

- `__class__` 查看对象所在的类

- `__dict__` 查看对象的属性,让所有的字段以字典的形式返回

- protobuf 嵌套类的使用

  ```python
  import addressbook_pb2
  
  
  def func():
      p = addressbook_pb2.Person
      p.name = "123"
      p.age = 456
      p.sex = "333"
      print(p.name)
      print(p.age)
      print(p.sex)
      print(type(p))
  
      c = addressbook_pb2.Person.Child() # 获取嵌套的Child类,带括号定义对象，不带括号获取的是一个类
      c.cname = "123"
      c.cage = 4444
  
      print(c.cname)
      print(c.cage)
  
  func()
  
  ```

- 异常

  - 当程序出现异常的时候会终止，如果不想终止就需要进行异常的捕获，然后进行处理，处理完整之后会继续进行下面的逻辑
  - raise 手动抛出异常
  - except进行异常的捕获处理

- enumerate

  - 比如for遍历list的时候，是无法获取下标的 ，使用enumerate就可以获取下标了

    ```python
    def func():
        list = [43, 432, 54, 43, 534, 5, 42, 3, 53]
        for k,v in list:
            print(k,v)
    ```

- 逻辑运算符

  - and 两边都为真的时候，选择and后面的内容分，and都为假的时候，选择前面的内容
  - or都为假的时候，选择后面的
  - 优先级
    - () > not  > and > or
  - in 在、not in 不在

- python3 和 python2区别

  - python2 有input 和 raw_input,python3只有input
    - raw_input 输入字符串不需要引号，input接收字符串需要引号，python3则进行了整合
  - python2 有int 和long ,python3 只有int

- 字符串是一个不可变的数据类型

  ```python
  name = "cccc"
  c = name.upper()
  print(name,c) # cccc CCCC
  
  # 可以看到原来的字符串并没有变
  
  ```

- itertools.product
  - 求笛卡尔积
  - [参考链接](https://www.cnblogs.com/xxxxxxxxx/p/11544432.html)

- collections.defaultdict(list)
  - 使用dict字典类型时，如果引用的key不存在，就会抛出KeyError。如果希望Key不存在时，返回一个默认值，就可以用defaultdict
  - [参考链接](https://www.cnblogs.com/pypypy/p/12093944.html)

- [字符串编码问题](https://wenku.baidu.com/view/edcec5d90ba1284ac850ad02de80d4d8d15a0166.html)

- 格式化 f -string

  ```python
  msg = f"姓名{name}" # 不需要使用占位符，直接使用f，里面用{}进行格式化即可
  print(msg)
  ```

- 类型区分

  ```python
  class Base:
      pass
  
  b =Base()
  print(type(b)) # <class '__main__.Base'> b 是Base的对象，所有打印出来的b的类型是Base
  print(type(Base)) # <class 'type'>，Base是type的对象，所以打印出来的类型是type
  ```

- **zip()** 函数用于将可迭代的对象作为参数，将对象中对应的元素打包成一个个元组，然后返回由这些元组组成的列表。如果各个迭代器的元素个数不一致，则返回列表长度与最短的对象相同，利用 * 号操作符，可以将元组解压为列表

  ```python
  # zip
  list = [1, 2, 3, 4, 5]
  z = zip(list)
  print(z) # zip
  print(type(zip)) # type
  print(*z) # (1,) (2,) (3,) (4,) (5,)
  
  # -------示例2----------
  # zip
  list = [1, 2, 3, 4, 5]
  list2 = ['a', 'b', 'c']
  z = zip(list, list2)
  print(z)  # zip
  print(type(zip))  # type
  print(*z)  # (1, 'a') (2, 'b') (3, 'c')
  
  
  
  ```

  

- extend
  - extend() 函数用于在列表末尾一次性追加另一个序列中的多个值（用新列表扩展原来的列表

- 函数前面带一短横
  - 希望这个函数只在类内或者是由其[子类](https://so.csdn.net/so/search?q=子类&spm=1001.2101.3001.7020)进行调用

- 单双引号
  - 都可以用来标识字符串
  - 双引号中不需要使用`\`来对字符进行转义(双引号除外)
  - 当字符串中出现双引号时，如果外层的引号为单引号则不需要使用转义字符

- `__getitem__`

  - **魔法函数**，当遍历类的时候就会调用这个方法

  ```python
  class Base:
  	def __init__(self,_name):
  		self.name = _name
  	
  	def __getitem__(self,index):
  		return self.name[index]			
  
  
  b = Base(['jim','tom','hah'])
  for i in b:
  	print(i)
  	
  	# jim
  	# tom
  	# hah
  ```

- `__all__`

  - python模块中的__all__，用于模块导入时限制，如：**from module import \***
  - 此时被导入模块若定义了__all__属性，则只有__all__内指定的属性、方法、类可被导入
  - 若没定义，则导入模块内的所有公有属性，方法和类。**也就是说通过all来控制跨文件访问**
  - **注意**
    - 只限制`import *` 全部导入时
    - 如果all中设置了，但是模块中没有定义会报错

