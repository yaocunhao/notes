- Flask框架会自动创建请求对象，后端返回的数据，框架不做修饰，直接返回
- 在 flask 应用中，我们只需要编写 view 函数，并不需要直接和响应（response）打交道，flask 会自动生成响应返回给客户端
- [参考链接](https://blog.csdn.net/weixin_44621343/article/details/117757244?ops_request_misc=&request_id=&biz_id=102&utm_term=Flask%20%E5%93%8D%E5%BA%94&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-3-117757244.142^v40^pc_rank_34_2,185^v2^control&spm=1018.2226.3001.4187)
  - Flask 的 `Response` 类非常简单，它只是继承了 `werkzeug.wrappers:Response`，然后设置默认返回类型为 html。不过从注释中，我们得到两条很有用的信息
    - 一般情况下不要直接操作 `Response` 对象，而是使用 `make_response` 方法来生成它
    - 如果需要使用自定义的响应对象，可以覆盖 flask app 对象的 `response_class` 属性

