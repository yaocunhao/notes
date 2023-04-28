[参考链接](https://www.jb51.net/article/224874.htm)

# 一、后单下划线例如: data_

- 防止和系统关键字重名，不建议使用

# 二、前单下划线例如: _data

- 提示程序员，这是内部使用点的变量，不要在外部使用，虽然可以使用![image-20220413205257259](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202206192309449.png)

#  三、前双下划线例如: __data

- 私有变量和函数，子类不能使用



# 四、前后双下划线: __data__

- python内部调用的



# 五、单下划线例如: _

- 表示一个临时的变量或者对象

# 六、python 封装性实现的原理

- Python 类中所有的属性和方法，都是公有（public）属性，如果希望 Python 底层修改类属性或者类方法的名称，以此将它们隐藏起来，只需将它们的名称前添加双下划线（“__”）即可

- python封装的底层实现其实是将其名字进行更改，导致找不到成员，所以实际还是能够访问到的

- 对于以双下划线开头命名的类属性或类方法，Python 在底层实现时，将它们的名称都偷偷改成了 "_类名__属性（方法）名" 的格式

  ```python
  class Test:
      def __init__(self, n='Tom'):
          self.__name = n
  
  
  t = Test()
  print(t._Test__name) #Tom 访问了变量
  print(t.__name) #不可访问
  
  ```

  

# 六、pylint 校验规范

- [参考链接](https://pylint.readthedocs.io/en/latest/user_guide/messages/error/bidirectional-unicode.html)