[参考链接](https://blog.csdn.net/u011146423/article/details/88191225?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522165957924516781790783100%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=165957924516781790783100&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-1-88191225-null-null.142^v39^pc_rank_34_2&utm_term=flask%20%E7%9A%84%20request&spm=1018.2226.3001.4187)

- 在flask 之中，由全局的 request 对象来提供请求信息

- 获取参数字典信息

  ```python
  args = request.values.to_dict()
  ```

  