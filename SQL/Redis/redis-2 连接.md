# 一、本地连接

- redis-cli

  ```python
  # 连接后输入PING 输出PONG 则表示联通成功
  127.0.0.1:6379> PING
  PONG
  ```



# 二、远程连接

- `redis-cli -h host -p port -a password`
  - -h：用于指定远程 Redis 服务器的 IP 地址；
  - -p：用于指定 Redis 远程服务器的端口号；
  - -a：可选参数，若远程服务器设置了密码，则需要输入

# 三、命令行自动提示功能

- Redis 拥有强大的命令行提示功能，支持`Tab`键自动补全