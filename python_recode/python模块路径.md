- 查看python查找包的路径

  ```python
  import sys
  print(sys.path)
  ```

- 如果自定义的包查找不到有三种办法

  - sys.append("path")
  - export PYTHONPATH
  - 将模块放在sys.path 已经包含的路径之下
  - **注意添加路径的时候不能加上包名本身**

  