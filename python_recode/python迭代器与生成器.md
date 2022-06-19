# 一、迭代器

- 可迭代器对象
  - 具有`__iter__`方法的就是可迭代对象
  - 粗略理解为能被for循环的就是可迭代对象

- 迭代器的优点

  - 节省空间 - 惰性机制

- 迭代器缺点

  - 使用不灵活，比如不能排序、追加等等
  - 一次性的，不能倒退

- 迭代器的使用方式

  - 可迭代对象只有`__iter__`方法
  - 迭代器具有`__iter__()`和`__next__()`方法

  ```python
  dic = {'a':1,'b':2,'c':3}
  
  new_dic = dic.__iter__() # 将可迭代对象转换成迭代器
  
  print(new_dic)
  print(new_dic.__next__()) # 从迭代器中取出对象
  print(new_dic.__next__())
  
  # <dict_keyiterator object at 0x7fa42a934b38>
  # a
  # b
  ```

- for循环本质就是不断的使用迭代器的next，因为for循环也是不可逆的

  ```python
  s = "12345"
  # for i in s:
  #     print(i)
  
  s_iter = s.__iter__()
  
  # print(s_iter.__next__())
  # print(s_iter.__next__())
  
  while True:
      try:
          print(s_iter.__next__())
      except StopIteration:
          pass
  
  ```

  ![image-20220405172605911](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202206192309496.png)

- 迭代器可以被for循环，循环。因为迭代器再取迭代器也是本身

  ```python
  s = "12345"
  s_iter = s.__iter__()
  
  for i in s_iter: # for就是取迭代器，然后执行iter.__next__()
      print(i)     # 迭代器取迭代器还是迭代器本身
  
  # 1
  # 2
  # 3
  # 4
  # 5
  ```

- python2 和 python3 的区别

  - `__iter__()` == iter()，即liter(list) 等价于 `list.__iter__()`
  - `__next__()` == next() 
  - python2之中没有`__iter__`方法，所以推荐使用iter()和next()

# 二、生成器

- 生成器的本质就是迭代器，迭代器有的生成器都有

- 迭代器和生成器本质的区别

  - 迭代器是python内置好的(只要符合规则就可以使用，比如next、iter)

  - 生成器是程序员人为定义的

  - 生成器有yield关键字

  - 通过对象内存地址查看是不一样的

    ```python
    def func():
        l = [1, 2, 3, 4, 5, 6]
        yield  from l
    
    g = func()
    l = [1,2,3]
    print(l.__iter__()) # <list_iterator object at 0x7fc954a54978> 迭代器
    print(g) # <generator object func at 0x7fda2a719990> 生成器
    
    ```

  - 通过send方法来判断，只有生成器有send方法

- 生成器的作用

  - 节省空间
  - 保留执行位置(比如第一次拿的是列表的第一个位置，第二次拿的是第二个位置)
  - 大量数据的时候，有时候不能全部传输，可以使用生成器保留位置，然后下一次继续进行传输

- 如何定义

  - 基于函数，当函数中有yield关键字的时候就是生成器
  - 基于生成器表达式

- 使用语法

  - yield 是将整个对象进行返回
  - yield from 是将可迭代对象逐个返回

- 实例演示

  - next 和 yield数量要一一对应，否则容易越界

    ```python
    def func():
        print(123)
        yield 456 # yield 是将值返回，，并且记录执行位置
        print(789)
    
    g = func() # 产生一个生成器
    
    # 触发生成器执行
    print(g.__next__()) # 123 456 现在生成器记录的位置为 yield 456 的位置
    
    # 继续向下执行
    print(g.__next__()) # 789 然后报错，这是因为next数量比yield多，导致越界了
    
    
    # ------正确写法----------
    def func():
        print(123)
        yield 456 # yield 是将值返回，，并且记录执行位置
        print(789)
        yield 1111
    g = func() # 产生一个生成器
    
    
    print(g.__next__()) # 123 456 现在生成器记录的位置为 yield 456 的位置
    print(g.__next__()) # 789 1111 # 最终在最后一个yield处停止
    
    ```

  - 把数据进行保存

    ```python
    def func():
        l = []
        for i in range(10):
            l.append(i)
            yield l
    
    
    g = func()
    print(next(g))
    print(next(g))
    print(next(g))
    
    # 生成器可以将数据进行保存
    # [0]
    # [0, 1]
    # [0, 1, 2]
    
    def func2():
        l = [i for i in range(10000)]
        for i in l:
            yield i # 进行阻塞停顿等待
    
    g = func2()
    print(g.__next__()) # 0
    print(g.__next__()) # 1
    ```

  - 生成器包装

    ```python
    def func():
        l = [1, 2, 3, 4, 5, 6]
        yield  from l
    
    g = func()
    for i in g: # for本质就是迭代器，并且内部带有异常捕获，因此不需要知道调用的生成器的包装个数
        print(i)
    ```

    