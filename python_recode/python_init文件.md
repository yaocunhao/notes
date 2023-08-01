- [link](https://www.jianshu.com/p/fed737eaaecd)

# init 文件的三大作用

- 初始化全局变量
  - 如果目录中包含了 `__init__.py` 时，当用 import 导入该目录时，会执行 `__init__.py` 里面的代码
- 控制模块导入
  - `_all__`关联了一个模块列表，当执行 from xx import * 时，就会导入**列表中的模块**
- 配置模块初始化工作
  - 本质上这就是一个正常的python文件，因此可以将初始化代码放入其中