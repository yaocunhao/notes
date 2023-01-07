- [必看参考链接](https://blog.csdn.net/qq_41061717/article/details/106139899?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522167288632716800184133379%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fall.%2522%257D&request_id=167288632716800184133379&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~rank_v31_ecpm-4-106139899-null-null.142^v68^wechat_v2,201^v4^add_ask,213^v2^t3_esquery_v2&utm_term=flask%20request.args%20%E5%92%8C%20request.get_json%E7%9A%84%E5%8C%BA%E5%88%AB&spm=1018.2226.3001.4187)
  
- [参考链接](https://blog.csdn.net/luocheng7430/article/details/81009911?ops_request_misc=&request_id=&biz_id=102&utm_term=flask%20request.get_data()&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-1-81009911.142^v68^wechat_v2,201^v4^add_ask,213^v2^t3_esquery_v2&spm=1018.2226.3001.4187)
  
- [参考链接](https://blog.csdn.net/u011146423/article/details/88191225?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522165957924516781790783100%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=165957924516781790783100&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-1-88191225-null-null.142^v39^pc_rank_34_2&utm_term=flask%20%E7%9A%84%20request&spm=1018.2226.3001.4187)
  
  - `request.args` 、`request.values.to_dict()`只支持GET方法
  
- 在flask 之中，由全局的 request 对象来提供请求信息

- 获取参数字典信息，**注意：只支持字典方法**

  ```python
  args = request.values.to_dict()
  ```

-  request.get_data() 获取数据(post也可以)

  - 但是这不是一个好办法，，(未完成待分析）
  
    

