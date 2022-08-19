- 模块
  - `from abc import ABC, abstractmethod`
- 使用方法
  - 所在的 class 继承 `abc.ABC`
  - 给需要抽象的实例方法添加装饰器 @abstractmethod

- 注意点
  - 如果基类没有继承`ABC`,则基类、子类都可以实例化，如果基类继承了ABC，则必须通过子类继承后进行实例化