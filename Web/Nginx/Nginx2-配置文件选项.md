# 一、模块分化

- 全局块：全局配置，对全局生效

- events块：配置影响Nginx服务器与用户的网络连接

- http块：配置代理，缓存，日志定义等绝大多数功能和第三方模块的配置

- server块：配置虚拟主机的相关参数，一个http块中可以有多个server块

  - 配置监听的端口等信息

  - server_name与host匹配优先级如下：

    ```
    1、完全匹配
    2、通配符在前的，如*.test.com
    3、在后的，如www.test.*
    4、正则匹配，如~^\.www\.test\.com$
    ```

  - 如果都不匹配，则:

    ```
    1、优先选择listen配置项后有default或default_server的
    2、找到匹配listen端口的第一个server块
    ```

  - **server_name 不会影响静态资源的访问，会影响是否能够访问代理服务器**

    - 比如两个不一样的域名，访问同一个静态资源，即使server_name 之中没有配置对应的域名，也不会影响
    - 但是在请求访问“Django”这种代理服务器时，就会出现错误![image-20220831103419112](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202208311034394.png)

- location块：用于配置匹配的uri

  - 当访问的是当前server快监听的端口时，如果url与location是对应的，则使用该模块进行代理
  - uwsgi_param
    - [uwsgi_param 官方文档](http://nginx.org/en/docs/http/ngx_http_uwsgi_module.html#uwsgi_param)
    - 设置一个应该传递给uwsgi服务器的参数。该值可以包含文本、变量和它们的组合,当且仅当，当前级别没有定义uwsgi_param指令时，这些指令从上一个配置级别继承
    - uwsig通用配置加载链接： https://github.com/nginx/nginx/blob/master/conf/uwsgi_params
  - uwsgi_pass： 表示要代理的 uWSGI服务器
    - sock文件或者设置的 upstream 
  - [include 的作用](https://blog.csdn.net/cmdd_/article/details/108622689?spm=1001.2101.3001.6661.1&utm_medium=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-108622689-blog-106530017.topnsimilarv1&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-108622689-blog-106530017.topnsimilarv1&utm_relevant_index=1)
    - 包含配置文件

- upstream：配置后端服务器具体地址，负载均衡配置不可或缺的部分

  - 如果没有写 upstream， 则可以可以更换写法为： uwsgi_pass unix:///dev/shm/map_apollo.sock (这里是uWSGI里面的socket通信套接字)![image-20220831103548693](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202208311035760.png)