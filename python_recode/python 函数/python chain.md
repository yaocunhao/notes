# 一、作用

- tertools.chain() 方法可以用来简化这个任务。 它接受一个可迭代对象列表作为输入，并返回一个[迭代器](https://so.csdn.net/so/search?q=迭代器&spm=1001.2101.3001.7020)，有效的屏蔽掉在多个容器中迭代细节

- 对多个对象执行相同的操作，且这几个对象在不同的容器之中

- 可以使代码更加的优雅，避免多个相同的循环逻辑

  ```python
  from itertools import c
  
  l = [1,2,3]
  d = {'name':'tom','age':18}
  
  ret = chain(l,d) # 返回迭代器
  print(type(ret))
  
  # 进行迭代器输出
  print(next(ret))
  print(next(ret))
  print(next(ret))
  print(next(ret))
  print(next(ret))
  
  # <class 'itertools.chain'>
  # 1
  # 2
  # 3
  # name
  # age
  ```

  