# 一、文件路径操作(os模块)

- [参考链接](https://blog.csdn.net/kdongyi/article/details/82313150?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522164964763616780366553189%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=164964763616780366553189&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_positive~default-1-82313150.142^v7^control,157^v4^control&utm_term=os.path&spm=1018.2226.3001.4187)

- 实例

  ```python
  import os
  
  TRAIL_DIR = os.path.abspath(
      os.path.join(os.path.dirname(__file__), '../../'))
  
  r1 = os.path.dirname(__file__) # 获取当前的路径 /Users/didi/PycharmProjects/pythonProject
  r2 = os.path.join(r1,"../../") # 进行路径合成   /Users/didi/PycharmProjects/pythonProject/../../
  r3 = os.path.abspath(r2) # 返回绝对路径
  print(r3) # /Users/didi
  ```

- [pathlib 模块](https://blog.csdn.net/looker53/article/details/106064166?spm=1001.2101.3001.6661.1&utm_medium=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1.pc_relevant_aa&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1.pc_relevant_aa&utm_relevant_index=1)
  - 新的方法模块，比OS模块更加的合理

# 二、文件内容操作(shutil模块)

- 对文件内容进行操作

- [参考链接](https://blog.csdn.net/qq_40223983/article/details/95984230)

# 三、文件压缩操作(tarfile)

- [参考链接](https://blog.csdn.net/yincheng_fans/article/details/94638828?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522164964964616780271915492%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=164964964616780271915492&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-2-94638828.142^v7^control,157^v4^control&utm_term=tarfile&spm=1018.2226.3001.4187)

