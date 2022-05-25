[mac使用参考链接](https://blog.csdn.net/abcnull/article/details/114403237?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522165278671416781483758217%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fall.%2522%257D&request_id=165278671416781483758217&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~rank_v31_ecpm-2-114403237-null-null.142^v10^control,157^v4^control&utm_term=mac+%E4%B8%8Bredis%E9%85%8D%E7%BD%AE%E6%96%87%E4%BB%B6%E5%9C%A8%E4%BB%80%E4%B9%88%E5%9C%B0%E6%96%B9&spm=1018.2226.3001.4187)

[最佳链接](https://blog.csdn.net/weixin_34332998/article/details/123568505?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522165337799216781483786111%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=165337799216781483786111&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-2-123568505-null-null.142^v10^pc_search_result_control_group,157^v8^control&utm_term=linux%E4%B8%8B%E5%AE%89%E8%A3%85redis&spm=1018.2226.3001.4187)

- 服务启动：`redis-server /usr/local/etc/redis.conf`

  - 本质就是用redis的bin下面的redis-server 来运行配置文件，进行服务的启动

- 查看是否启动：`ps aux | grep redis-server`

- 关闭服务：

  ```python
  127.0.0.1:6379> shutdown
  not connected> exit
  ```

- 远程连接
  - 在redis.conf文件之中
    - `注释 bind:127.0.0.1`
    - 设置密码：`requirepass后面添加密码`





# 一、详细操作

[最佳链接](https://blog.csdn.net/weixin_34332998/article/details/123568505?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522165337799216781483786111%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=165337799216781483786111&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-2-123568505-null-null.142^v10^pc_search_result_control_group,157^v8^control&utm_term=linux%E4%B8%8B%E5%AE%89%E8%A3%85redis&spm=1018.2226.3001.4187)

- 安装
  - [redis版本库](https://download.redis.io/releases/)
  - tar -xzvf xxx 解压
  - make 编译
  - redis.conf 在redis解压后的根目录下
  - ./redis-master-server redis.conf 后台启动

- 一些配置的改变
  - 开启远程连接注意
    - 默认 `protected-mode yes` 保护模式时开启的，如果你需要远程连接请将他设置为 `protected-mode no`
    - 默认 `bind 127.0.0.1` 是没有注释的，如果你要开始远程连接可以注释 `#` 他，或者指定IP
    - 设置密码：`requirepass后面添加密码`
    - 默认情况下 `daemonize no` 是不会作为守护进程运行的，如果需要设置为守护进程方式运行（后台运行）则将其改为 `daemonize yes`