# 参考链接

- 官方文档
  - [中文官方文档](https://blog.redis.com.cn/doc/example/loadbanlance.html)
  - [英文官档](http://nginx.org/en/docs/http/ngx_http_upstream_module.html#upstream)
- [git参考链接](https://github.com/dunwu/nginx-tutorial)
  - 文档最下有各种资源

- [Nginx排错和日常维护](https://cloud.tencent.com/developer/article/1886674)


# 参考2

- [c](https://blog.csdn.net/qq_42327944/article/details/124679374?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522166191002516781667835856%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fall.%2522%257D&request_id=166191002516781667835856&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~pc_rank_34-5-124679374-null-null.142^v42^pc_rank_34,185^v2^control&utm_term=%E6%B5%8B%E8%AF%95Nginx%20%E6%80%8E%E4%B9%88%E4%B8%B4%E6%97%B6%E8%8E%B7%E5%8F%96%E4%B8%80%E4%B8%AA%E5%9F%9F%E5%90%8D&spm=1018.2226.3001.4187)

  - nginx.conf

    1、全局块：全局配置，对全局生效；

    2、events块：配置影响Nginx服务器与用户的网络连接；

    3、http块：配置代理，缓存，日志定义等绝大多数功能和第三方模块的配置；

    4、server块：配置虚拟主机的相关参数，一个http块中可以有多个server块；

    5、location块：用于配置匹配的uri；

    6、upstream：配置后端服务器具体地址，负载均衡配置不可或缺的部分。



# 参数调优

- gzip
  - 将需要传输的数据进行压缩。 压缩级别越高压缩之后的体积越小，但是消耗的CPU资源也越多