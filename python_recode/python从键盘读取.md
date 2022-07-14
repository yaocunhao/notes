# 一、input

- **注意最后得到的值为string**

  ```python
  ret = input('请输入一个数\n')
  print(ret, type(ret))
  # 请输入一个数
  # 123
  # 123 <class 'str'>
  
  
  # 请输入一个数
  # 1+2
  # 1+2 <class 'str'>
  ```

# 二、eval

- eval()函数用来**执行一个字符串表达式，并返回表达式的值**。还可以把字符串转化为list、tuple、dict

  ```python
  ret = eval(input('请输入一个数\n'))
  print(ret, type(ret))
  
  # 执行运算
  # 请输入一个数
  # 1+2
  # 3 <class 'int'>
  
  # 执行转换
  # 请输入一个数
  # str(123)   
  # 123 <class 'str'>
  ```

  

# 三、通过map函数传入多个参数

- 需要注意的是，map参数中，第一个参数为函数名称，第二个参数为迭代器，迭代器的每一个元素都执行第一个参数指向的函数。最后返回一个map迭代器对象，可以将其转换成列表

  ```python
  ret = map(int,input('请输入x,y做标，以空格分割\n').split(' ')) 
  for i in ret:
    print(i)
  
  # 请输入x,y做标，以空格分割
  # 123 456
  # 123
  # 456
  ```

  

# 四、读取一行

```python
import sys

ret = sys.stdin.readline().strip() # strip 去掉前后的空格
print(ret)
# dadadadadadadad adsdasdasd dasdadas
# dadadadadadadad adsdasdasd dasdadas
```

