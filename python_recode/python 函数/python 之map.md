# 一、基本介绍

- 是什么
  - map() 会根据提供的函数对指定序列做映射。第一个参数 function 以参数序列中的每一个元素调用 function 函数，返回包含每次 function 函数返回值的新列表
  - 第一个参数接受一个函数名，后面的参数接受一个或多个可迭代的序列，返回的是一个集合。把函数依次作用在list中的每一个元素上，得到一个新的list并返回。注意，map不改变原list，而是返回一个新list
- 语法
  - map() 函数语法：**map(function, iterable, …)**
    - function – 函数
    - iterable – 一个或多个序列
  - 返回值
    - Python 2.x 返回列表
      Python 3.x 返回迭代器

- 示例

  ```python
  # 传入单个参数
  def func(num):
  	return num**2
  def map_test(): 
  	l = [1,2]
  	l2 = [4,5]
  	ret = map(func,l) # 迭代器对象
  	print(list(ret)) # 转换成列表 [1,4]
  
  # 传入多个参数
  def map_test(): 
  	l = [1,2]
  	l2 = [4,5,6]
  	ret = map(lambda x,y:x+y,l,l2)
  	print(list(ret)) # 转换成列表 [1,4]
  
  map_test() # [5, 7]
  
  ```

  