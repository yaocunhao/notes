- [参考链接](https://blog.csdn.net/wan212000/article/details/121534699?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522165885101916780366523374%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=165885101916780366523374&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~baidu_landing_v2~default-1-121534699-null-null.142^v35^experiment_2_v1,185^v2^control&utm_term=python%20%E6%80%8E%E4%B9%88%E5%AF%BC%E5%85%A5%E8%87%AA%E5%AE%9A%E4%B9%89%E6%A8%A1%E5%9D%97&spm=1018.2226.3001.4187)

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

  