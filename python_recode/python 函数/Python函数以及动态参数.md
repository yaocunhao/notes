## 传参问题

- python进行传参的时候，是默认使用引用的

## 动态参数

- 形参聚合

  - *args：元组，接收**多余的位置参数**
  - **kwargs：dict{}传入，接收 多余的关键字参数**，但是传参的时候格式为func(a=100,b=200) 这种形式
  - 函数体中的args、kwargs可以进行打散
  
- 实参也可以打散进行传入

- 实例

  - 形参的聚合与打散

  ```python
  # 形参聚合与打散
  def func(*args, **kwargs):
      # 位置参数的聚合与打散
      print(args)  # (1, 2, 3, 4, 5)
      print(*args)  # 1 2 3 4 5
  
      print(kwargs)  # {'a': 1, 'b': 2, 'c': 3}
      
      # 关键字参数的聚合
      print(kwargs.keys()) # dict_keys(['a', 'b', 'c'])
      print(kwargs.values()) # dict_values([1, 2, 3])
      
      # 关键字参数的打散
      print(*kwargs.keys()) # a b c
      print(*kwargs.values())  # a b c
  
  
  func(1, 2, 3, 4, 5, a=1, b=2, c=3)
  
  ```

  - 实参的打散  

    ```python
    def func(*args, **kwargs):
        print(args, "_", kwargs)
    
    
    l = [1, 2, 3, 4, 5]
    dic = {'a': 1, 'b': 2}
    t = (1, 2, 3, 4, 5)
    
    func(*t) # (1, 2, 3, 4, 5) _ {} # 元组也可以被打散
    func(l, dic)  # ([1, 2, 3, 4, 5], {'a': 1, 'b': 2}) _ {},正常传入，就是同一个元组的两个参数
    func(*l, *dic)  # (1, 2, 3, 4, 5, 'a', 'b') _ {},全部被打散传入了
    func(*l, **dic)  # (1, 2, 3, 4, 5) _ {'a': 1, 'b': 2}，两个*被打散成dict传入的
    
    ```

  - 小结

    -  函数参数
      - `*args`**接收全部多余的非关键字传参构成一个元组**,`**kwargs`接收全部多余的关键字传参，由于python规定非关键字传参在关键字传参前面，所以`*args`必须写在`**kwargs`之前
    - 函数体内使用
      - args为元组，kwargs为字典
      - `*args`将元组打散，`*kwargs`获取字典的key键，不加`*`则是输出元组和字典
    - 在传参时
      - 前面带`*`则是进行打散，不带则是整体传入，`**`则是传入字典。**特别需要注意的是，传入dict的时候，需要带`**`如果不带，前面有`*args`时，dict会被当做一个整体被args接收**

- 参数优先级

  - 位置 > 动态位置 > 默认 > 动态关键字

    ```python
    def func(a,b,c,*args,d=1,**kwargs)
    ```

## 函数的命名空间

- 内置空间：存放python自导的内容
  - 比如print、input等等
  
- 全局空间：py文件中

  - **在python中的`if 语句`、`for语句`、`while语句`定义的就是全局变量[参考链接](https://blog.csdn.net/qq_37266079/article/details/103888112)**

- 局部空间：函数体中的空间

- 加载顺序
  - 内置 > 全局 >  局部

- 取值顺序
  - 局部 > 全局 > 内置![image-20220404162223810](../图片记录/image-20220404162223810.png)

- 作用域

  - 全局作用域：内置空间 + 全局空间

  - 局部作用域： 局部空间

  - global 申请修改全局的变量，**当全局中没有变量时，可以创建**

    - print(globals()) ，查看全局变量空间

      ```python
      a = 1000
      print(globals()) # {'a': 1000},以字典的形式存在
      
      ```

    - 局部可以使用全局或者内置的，**但是不能修改**。修改全局变量的时候需要global进行声明

      ```python
      a = 1000
      print(id(a))
      
      
      def func():
          global a  # 申明需要修改
          print(id(a))
          a = a - 10000  # 修改,修改之后a不是原来的a了，因为重新引用了一个值
          print(id(1))
          print(a)
      
      
      func()
      ```

    - 全局中不存在，进行创建

      ```python
      def func():
          global a
          a = 100
          print(locals()) # 查看局部变量 {} 为空
      
      func()
      print(a) # 100 
      ```

  - nonlocal

    - *nonlocal声明的变量不是局部变量,也不是全局变量,而是外部嵌套函数内的变量*

    - 在局部内，修改距离nonlocal最近的上一层，如果上一层没有，就继续往向上查找

    - nonlocal 本层内不能定义申明的变量

      ```python
      a = 100
      
      
      def func1():
          a = 200
          def func2():
              nonlocal a
              a += 100 # 修改的是func1的a
              def func3():
                  print(a) # 这里没有声明，因此不能对a进行修改  
              func3()
          func2()
          print(a)
      
      func1()
      print(a)                    
      
      # 300 300 10
      ```

      

## 函数名的使用

- 函数名可以当做值被赋值

- 函数名可以当做两个函数的返回值

- 函数名可以当做另外一个函数的参数

- 函数名可以当做值存到容器之中

  ```python
  # 函数名当做参数
  def func():
      print("i am here!")
  a = func
  a()
  
  
  # 函数名当做函数的返回值
  def func2():
      return func
  a = func2()
  a()
  
  
  # 函数名当做另外一个函数的参数
  def func3(f):
      f()
  func3(func)
  
  
  # 函数名存储在容器之中
  l =[func]
  t =(func,)
  d = {'f':func}
  l[0]()
  t[0]()
  d['f']()
  ```

## 匿名函数

- lambda 表达式，又称匿名函数，常用来表示内部仅包含 1 行表达式的函数。如果一个函数的函数体仅有 1 行表达式，则该函数就可以用 lambda 表达式来代替

- lambda 表达式的语法格式如下：

  - name = lambda [list] : 表达式

  - 其中，定义 lambda 表达式，必须使用 lambda 关键字

  - [list] 作为可选参数，等同于定义函数是指定的参数列表

  - name 为该表达式的名称

    ```python
    # 下面的x,y就是函数的参数，:后面就是函数体
    add = lambda x,y:print(x+y)
    add(10,20)
    
    add = lambda :print(200000)
    add()
    ```

    

  



## 内置函数

### 不常用

- 拿来主义只要知道怎么用的就行 

- all() 判断里面的元素是否都为真，返回的是bool值

- any() 判断元素的里面是否有一个是真的，返回的是bool值

- bytes() 数据类型

  ```python
  print(bytes("你好"，encoding="utf-8")) # 将str转成字节
  ```

- callable() 判断是否可以调用

- chr() 查找当前文字

  - chr(89) 通过码位找当前的文字

- ord() 查找当前编码

  - ord("1231")

- complex() 复数

- divmod(5，2)  商余

- eval() exec() 执行一个字符串形式的 Python 代码（代码以字符串的形式提供），相当于一个 Python 的解释器

- frozenset() 冻结集合

- help() 帮助，获取函数的解释比如help(list.pop)

- id()

- globals() 获取全局变量

- locals() 获取局部变量

- hash() 获取哈希值

- otc() 将十进制转换成八进制

- repr() 展示出实际类型 

- round(num,step)  #保留step位小数

### 常用

- sort() 排序，比如list.sort()

  - 在原地进行修改
  - [自定义排序函数](https://blog.csdn.net/qq_43559598/article/details/106039213?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522168112680816800182784497%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fall.%2522%257D&request_id=168112680816800182784497&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~rank_v31_ecpm-5-106039213-null-null.142^v82^insert_down1,201^v4^add_ask,239^v2^insert_chatgpt&utm_term=python%20sort%E5%8F%AF%E4%BB%A5%E4%BC%A0%E5%85%A5%E6%AF%94%E8%BE%83%E5%87%BD%E6%95%B0%E5%90%97&spm=1018.2226.3001.4187)

- reverse() 反转，比如list.reverse，在原地修改

- abs() 取绝对值

- format() 格式化

  ```python
  s = 10
  format(s,"b") # 将十进制格式化为2进制
  # o - 8进制
  # x - 16进制
  # f - 小数 ，保留小数位的格式为 ->保留两位小数 format(s,".2f")
  format(s,"08x") #转换成八进制，不够八位用零填充
  
  print(format(s,>20)） #右对齐、< 左对齐、^ 居中
  ```

- enumerate() 枚举

  - 前面参数为可迭代的
  - 后面参数为下标从多少开始

  ```python
  l = [1,2,3]
  for i in enumerate(l):
      print(i)
  
  for i in enumerate(l,2):
      print(i)
  
  # 带下标打印，默认取值为0
  # (0, 1)
  # (1, 2)
  # (2, 3)
  # 
  # (2, 1)
  # (3, 2)
  # (4, 3)
  ```

- reversed(可迭代空间) 返回一个反转的迭代器

  - 开辟新空间，元数据不受影响

  ```python
  l = [1,2,3]
  print(list(reversed(l))) # [3, 2, 1]
  ```

- sum() 求和

  - 纯数字，不能用于字符串，因为起始值为数字不能和字符换相加

  ```python
  t = (1, 2, 3, 4)
  print(sum(t))
  print(sum(t,2))  # 从2开始
  
  # 10
  # 12
  ```

- zip() 拉链

  - 默认按照短的来拉

  ```python
  l1 = [1,2,3,4,5,6]
  l2 = [0,0,0,0]
  
  print(list(zip(l1,l2)))
  # [(1, 0), (2, 0), (3, 0), (4, 0)]
  ```

- dir() 查看当前内容有什么方法，比如dir（list）

- print(sep,end)

  - sep 分割符，默认美哦与
  - end 默认换行
  - file 将内容输出到文件之中

#### 高阶函数

- [参考链接](https://www.cnblogs.com/littlefivebolg/articles/9094942.html)

- 高阶函数
  - 一个函数可以作为参数传给另外一个函数，或者一个函数的返回值作为另外一个函数(若函数的返回值为函数本身，则为递归)，满足其一则为高阶函数

- 高阶函数默认实现了一个for循环

- filter() 过滤 

  - filter(函数就是过滤条件，可迭代对象)

  - filter传入的函数的返回值为布尔值

    ```python
    l = [1, 2, 3, 4, 5, 6, 7]
    
    def func(x):
        return x > 4
    
    print(list(filter(func, l)))  # [5, 6, 7]
    
    print(list(filter(lambda x:x<3,l)))
    
    # filter原理
    def filter(func, iter):
        new_lst = []
        for i in iter:
            if func(i) == True:
                new_lst.append(i)
        return new_lst
    
    ```

- map() 映射

  - map接收的是两个参数一个函数，一个序列，其功能是将序列中的值处理再依次返回列表之中。其返回值为一个迭代器对象

  - map(函数，可迭代对象)

    - 一个可迭代对象对应一个参数c

    ```python
    l = [1, 2, 3, 4, 5]
    print(list(map(lambda x: x + 1, l)))
    # [2, 3, 4, 5, 6]
    
    l1 = [1, 2, 3, 4, 5]
    l2 = [1, 2, 34, 4, 5, 6]
    l3 = [2, 3, 45, 35, 3]
    print(list(map(lambda x, y, z: x + y + z, l1, l2, l3)))
    ```

- reduce 函数

  - reduce函数不是内置函数，而是在模块functools中的函数，故需要导入(python2之中可以直接使用)

  - reduce函数也是一个参数为函数，一个参数为可迭代对象的高阶函数，其返回值为一个值而不是迭代器对象，故其常用于叠加，叠乘等等

    ```python
    from functools import reduce
    
    l = [1, 2, 3, 4, 5]
    print(reduce(lambda x, y: x + y, l))
    
    
    # 模拟reduce原理
    def reduce_test(func, array, ini=None):  # ini为起始的基数
        if (ini == None):
            ret = array.pop(0)  # 基值为第一个值，并且从可迭代对象之中pop出来，也就是从0开始进行迭代
        else:
            ret = ini
        for i in array:  # 遍历可迭代对象
            ret = func(ret, i)
        return ret
    ```

- sorted() 排序，不再局限于list

  - sorted(可迭代对象，key=函数(排序规则),reverse=bool)

  - 先用函数对元素进行处理之后，再进行排序 v

    ```python
    l = [-312, 32, 43, 245, 43, 5, 43, 5, 32]
    print(sorted(l, key=abs))
    # [5, 5, 32, 32, 43, 43, 43, 245, -312]
    
    #eg2:
    l = [{'id': 1, 'age': 18}, {'id': 2, 'age': 17}, {'id': 2, 'age': 16}]
    print(sorted(l, key=lambda x: x['age'],reverse=True))
    #  [{'id': 1, 'age': 18}, {'id': 2, 'age': 17}, {'id': 2, 'age': 16}]
    ```

- max(列表，key),最大值。不建议使用key
- min(),最小值

- type() 函数

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

      