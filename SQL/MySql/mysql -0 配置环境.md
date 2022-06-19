# 一、下载资源寻找

- 从[mysql官网](https://dev.mysql.com/downloads/mysql/5.7.html#downloads)找到对应的版本，wget 拉取下来
- `wget https://dev.mysql.com/get/Downloads/MySQL-5.7/mysql-5.7.38-linux-glibc2.12-x86_64.tar.gz`![image-20220524211229345](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202206192310124.png)

- 解压之后得到文件夹，这里面就是对应的mysql程序

  ```bash
  ych@2ec71362f5e6:~/mysql-5.7.38-linux-glibc2.12-x86_64$ ls
  bin  docs  include  lib  LICENSE  man  README  share  support-files
  ```

# 二、myqsl 配置文件

- 一般将配置文件放在/etc下

  ```bash
  [mysqld]
  bind-address=0.0.0.0
  port=3306
  user=mysql # 用户
  basedir=/home/ych/mysql-5.7.38-linux-glibc2.12-x86_64 # 安装路径
  datadir=/data/mysql # 数据目录
  socket=/tmp/mysql.sock 
  log-error=/data/mysql/mysql.err # 错误日志目录
  pid-file=/data/mysql/mysql.pid
  
  #character config
  character_set_server=utf8mb4
  symbolic-links=0
  explicit_defaults_for_timestamp=true
  ```

- 创建文件

  - /data/mysql

# 三、初始化

- 初始化命令

  - 进入到安装目录下的bin之中

    ```mysql
    ./mysqld --defaults-file=/etc/my.cnf  --user=root --initialize
    ```

- 查看密码

  ```mysql
  cat /data/mysql/mysql.err
  
  # 该日志之中会出现root初始密码
  ```

  - **注意：**[如果此时报错找不到error文件参考链接](https://blog.csdn.net/weixin_34365635/article/details/93424083)

- 启动mysql

  - 第四点讲述

- 登录myql

  ```
  ./mysql -uroot -p
  ```

- 修改初始密码

  ```mysql
  SET PASSWORD = PASSWORD('123456'); # 设置当前用户密码
  ALTER USER 'root'@'localhost' PASSWORD EXPIRE NEVER; # 永不过期
  FLUSH PRIVILEGES;                                 # 刷新权限
  ```

- 远程连接

  - 修改mysql.user 表之中的数据

    ```python
    use mysql                                            #访问mysql库
    update user set host = '%' where user = 'root';      #使root能再任何host访问
    FLUSH PRIVILEGES;                                    #刷新
    ```

# 四、启动方式

- 启动方式一：

  - 在bin目录下有 mysqld 可执行文件

    ```mysql
    启动mysql 
    sudo mysqld --defaults-file=/etc/my.cnf --user=root
    
    关闭mysql 
    mysqladmin -uroot -p shutdown 
    ```

- 启动方式二：
  - service.mysql start
    - 也就是说service start 本质就是调用的这种方式
  - 这种方式需要将`mysql-5.7.38-linux-glibc2.12-x86_64/support-files/mysql.server` 放置到PATH路径中的文件夹下面
  - 所以可以直接 ./mysql.server start

# 五、参考链接

- [安装参考链接](https://blog.csdn.net/qq_37598011/article/details/93489404?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522165339277516782395390001%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fall.%2522%257D&request_id=165339277516782395390001&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~hot_rank-1-93489404-null-null.142^v10^pc_search_result_control_group,157^v12^control&utm_term=linux+%E4%B8%8B%E5%AE%89%E8%A3%85mysql&spm=1018.2226.3001.4187)
- [找不到文件](https://blog.csdn.net/weixin_34365635/article/details/93424083)
- [找不到socket](https://blog.csdn.net/weixin_34112900/article/details/93854948?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522165344849516781818769740%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=165344849516781818769740&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-1-93854948-null-null.142^v10^pc_search_result_control_group,157^v12^control&utm_term=+Cant+connect+to+local+MySQL+server+through+socket+%2Ftmp%2Fmysql.sock+%282%29&spm=1018.2226.3001.4187)
  - 一般是修改了mysqld的sockt文件位置没修改client和mysql的socket文件位置