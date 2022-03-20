- isinstance() 函数
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

-  if not
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

- 参数带*号
  - 单星号：*agrs，将所有参数以元组(tuple)的形式导入
  - 双星号：**kwargs，将参数以字典(dict)的形式导入