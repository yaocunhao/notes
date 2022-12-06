# 一、python名称空间

- python通过名称空间来搜索当前环境(局部或是全局)中的变量
- 名称空间**本质上是一个字典**



# 二、locals()

- 每个函数都有自己的名称空间，它记录了函数的参数和局部定义的变量,方法等

  ```python
  def test_func(a,b):
  	def sum(a,b):
  		return a+b
  	
  	ret = sum(a,b)*2
  	print(locals()) # 打印名称空间：本质就是一个字典
  	return ret
  
  test_func(10,20)
  
  # {'ret': 60, 'sum': <function test_func.<locals>.sum at 0x7fad0eae2268>, 'b': 20, 'a': 10}
  ```

  

# 三、globals()

- 在每个模块之中都会记录自己的全局变量，**其中包括函数、类、导入的模块、模块级别的变量和常量**



## 四、内置名称空间

- 何模块都可以访问，它存放在内置的函数(built−infunctionbuilt−infunction)和异常(Built−inExceptionBuilt−inException)![image-20220619230812483](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202206192308557.png)