- [参考链接](https://blog.csdn.net/zhangquan_zone/article/details/77627899?spm=1001.2101.3001.6650.1&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-77627899-blog-124009940.t5_download_50w&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-77627899-blog-124009940.t5_download_50w&utm_relevant_index=2)
- [参考链接2](https://blog.csdn.net/Oh_Python/article/details/123756400?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522166357861916782425178502%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=166357861916782425178502&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-1-123756400-null-null.142^v47^pc_rank_34_1,201^v3^add_ask&utm_term=Django%20%E8%AE%BE%E7%BD%AEcookies&spm=1018.2226.3001.4187)

# 基本介绍

- cookies 是服务器返回给游览器的，游览器将其进行了存储。**游览器自动进行存储，在 下次请求的时候自动发送**
- cookies 之中包含了几个比较重要的字段信息
  - 比如什么时候删除、发送给那个域名、设置的过期时间

# 参数介绍

## 参数列表

| key      | value                                                        | 解释                                |
| -------- | ------------------------------------------------------------ | ----------------------------------- |
| max_age  | 整形：秒                                                     | cookie有效期                        |
| expires  | 当设置为整型时表示cookie有效期（单位是秒），当设置为datetime类型时表示cookie的截止期 | cookie有效期                        |
| path     | 表示cookie所在的路径                                         | '/'表示cookie存放于根路径           |
| domain   | 'lqmyx.com', 该域名下的所有域名www.lqmyx.com/api.lqmyx.com/meinv.lqmyx.com/static/lq都能够访问到cookie | 同域内游览器会自动读取和发送cookies |
| secure   | 值的类型为布尔类型，当值为True，只允许https对cookie进行访    | 安全性设置                          |
| httponly | 值类型为布尔类型，当值为True，表示不允许前端的js获取cookie   | 安全性设置                          |
| samesite | lax或strict时，表示不允许携带cookie进行跨源的请求            | 安全性设置                          |

## 过期时间参数

- 当在配置cookie时即不设置max_age也不设置expires，即表示会话cookie，也就是关闭浏览器再次打开之后，就没有该cookie值了
- expires
  - expires可接收三种类型的参数：
    - int
    - 格林威治时间格式：'Week, Month-Day-Year Hour:minute:second GMT'，Week和Month要用英文缩写。例如 expires='Wed, Aug-3-22 22:01:00 GMT'
    - datetime对象：例 expires=datetime(2022, 3, 26, 11, 0, 0)，expires=datetime(年, 月, 日, 时, 分, 秒)
  - 当设置expires为格林威治时间或datetime类型时，表示的是截止时间**，且截止时间会比我们的时间多8个小时**
- max_age 和 expires 同时存在的情况
  - 当expires的时间格式为非datetime时，使用的是max_age的值
  - 当expires的时间格式为datetime时，使用的是expires的值