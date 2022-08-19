# 一、继承的查找顺序

- 对象 > 父类 > 父父类

- 查找的顺序为mro顺序

  - mro列表查找准则
    - 子类先查，再查父类
    - 当继承多个父类的时候，按mro列表顺序被检查
    - 如果继承多个类，被继承类内具有相同的方法，**先输出mro列表左边类的方法**
    - 注意：mro列表可以写成 `__mro__` 也可以，调用mro方法的必须是起始类，obj是D的对象，所以用D.mro()
    - mro列表是通过一个C3线性算法来实现的
  - 如下述菱形继承之中，的查找顺序为
    - 先找对象A的命名空间，然后按继承顺序进行查找(**先查找继承的左边类**)，对继承的父类查找依然按照此规则![image-20220627005553461](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202206270055781.png)

  ```python
  class A():
    def out_text(self):
      print('from A')
  
  class B(A):
    def out_text(self):
      print('from B')
  
  class C(A):
    def out_text(self):
      print('from C')
  
  class D(B, C):
    pass
  
  obj = D()
  obj.out_text()  # -> form B
  print(
      D.mro()
  )  # [<class '__main__.D'>, <class '__main__.B'>, <class '__main__.C'>, <class '__main__.A'>, <class 'object'>]
  
  ```



# 二、多继承冗余问题

- 多继承冗余问题：根据子类继承多个父类时这些父类的前后次序决定，**即排在前面父类中的类方法会覆盖排在后面父类中的同名类方法**

- **重写调用父类成员问题：直接使用父类的名称**进行调用(**python就是一个命名空间**)，但是需要手动的传入self,称之为未绑定方法

  ```python
  class People:
      def __init__(self, name='Jim'):
          self.__name = name
  
      def getname(self):
          print(self.__name)
  
  
  class Child(People):
      def getname(self):
          print("i not name")
  
  
  c = Child()
  c.getname()  # i not name
  People.getname(c)  # Jim,非绑定用法，子类重写了父类的成员，想调用父类的方法，就使用父类的名称进行调用
  
  ```



# 二、super()方法

## 2.1 基本介绍

- `super()` 方法的存在就是用于解决多继承问题，在一个父类中使用该方法用于**调用下一个父类的方法**

- 当进行多继承的时候，**python默认只会调用第一个继承的父类的构造函数**，因此需要子类手动调用父类的构造函数

  ```python
  class Beef:
      def __init__(self, pe):
          self.price = pe
  
      def get_prive(self):
          print(self.price)
  
  
  class Fruit:
      def __init__(self, num):
          self.number = num
  
      def get_num(self):
          print(self.numbers)
  
  
  class Food(Beef, Fruit):
      pass
  
  
  # 错误使用
  # 只会默认调用第一个父类的构造函数，因此传入两个参数时是错误的
  f = Food(312, 542354) #不接受两个参数
  ```

## 2.2 使用方法

- 类可以看做一个独立空间，在类的外部调用其中的实例方法，可以向调用普通函数那样，只不过需要额外备注类名（此方式又称为未绑定方法）

- 使用 super() 函数

  - 但如果涉及多继承，该函数只能调用第一个直接父类的构造方法，**也就是说涉及到多继承时，在子类构造函数中，调用第一个父类构造方法的方式有以上 2 种，而调用其它父类构造方法的方式只能使用未绑定方法**

  ```python
  class Beef:
      def __init__(self, pe):
          self.price = pe
  
      def get_prive(self):
          print(self.price)
  
  
  class Fruit:
      def __init__(self, num):
          self.number = num
  
      def get_num(self):
          print(self.number)
  
  
  class Food(Beef, Fruit):
      def __init__(self, price, num):
          super().__init__(price)  # 调用Beef的构造函数可以使用两种方式
          Fruit.__init__(self, num)  # 调用Fruit的构造函数只可以使用一种方式
  
  
  # 在Food的构造函数之中手动调用父类的构造函数，这样在初始化的时候才不会发生错误
  f = Food(312, 542354)
  f.get_prive()  # 312
  f.get_num()  # 542354
  ```

## 2.3 注意事项

- 不能在就旧式类中进行调用

  - python3x 往后都是新式类，如果没有写明继承某个类，都默认继承了object这个类

- 混用super与显式类调用，如下代码会导致B类被初始化两次

  ```python
  class A:
  
    def __init__(self):
      print("A", end=" ")
      super().__init__()  #会初始化B，导致B多初始化一次,因为super作用就是调用下一个父类的构造函数，C的下一个父类就是B
  
  
  class B:
  
    def __init__(self):
      print("B", end=" ")
  
  class C(A, B):
  
    def __init__(self):
      print("C", end=" ")
      A.__init__(self)
      B.__init__(self)
  
  C() # C A B B 
  
  # C的进行构造函数的调用
  # 然后初始化父类A，但是父类A中使用super函数，因此会调用C的下一个父类的构造函数也就是B
  # 然后C构造函数中再次调用了B的构造函数，因此导致B被初始化了两次
  
  # 正确的方法为
  class C(A, B):
      def __init__(self):
          print("C", end=" ")
          super().__init__() #调用，初始化两个类
  
  # super初始化A，A中的super再初始化B
  ```

- 不同种类的参数

  - 如果子类和基类的构造函数的参数不同，调用基类的时候就会出现问题

    ```python
    class A:
    
      def __init__(self):
        print("A", end=" ")
        super().__init__()
    
    
    class B(A):
    
      def __init__(self):
        print("B", end=" ")
        super().__init__()
    
    
    class C(A):
    
      def __init__(self):
        print("C", end=" ")
        super().__init__()
    
    
    class D(B, C):
    
      def __init__(self, arg):
        print("D", end=" ")
        super().__init__(arg)
    
    D(123) # 出现错误，因为D继承的父类的构造函数并没有参数
    
    # 解决办法使用 *args 和 **kwargs 包装的参数和关键字参数
    # 但是这种方法会导致构造函数可以接收任意类型的参数，代码健壮性不高
    ```

- 小结

  - 

  - - 尽可能**避免使用多继承**，可以使用一些[设计模式](http://c.biancheng.net/design_pattern/)来替代它；

    - super 的使用必须一致，即在类的层次结构中，要么全部使用 super，要么全不用。**混用 super 和传统调用是一种混乱的写法**

    - 如果代码需要兼容 Python 2.x，在 Python 3.x 中应该显式地继承自 object。在 Python 2.x 中，没有指定任何祖先地类都被认定为旧式类

    - 调用父类时应提前查看类的层次结构，也就是使用类的 __mro__ 属性或者 mro() 方法查看有关类的 MRO

      ```\
      print(D.__mro__)
      #(<class '__main__.D'>, <class '__main__.B'>, <class '__main__.C'>, <class '__main__.A'>, <class 'object'>)
      ```

  



