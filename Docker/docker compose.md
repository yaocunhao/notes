- docker compose  批量启动容器

# 一、启动mysql

[官方文档](https://hub.docker.com/_/mysql)

- docker-compose文件

  ```python
  version: "3.9"
  
  services:
  
  
    mysql:
      image: mysql:5.7 # 使用镜像
      container_name: map_mysql # 容器名称
      restart: always # 自动重启
      ports:
        - "6666:3306" # 端口映射
      networks:
        - default
      command: --default-authentication-plugin=mysql_native_password # 校验插件
      volumes: # 进行文件的挂载，这样下次再登录的时候就可以看到
        - /home/didi/docker_test2/mysql.conf:/etc/mysql/my.cnf # 配置文件挂载
        - /home/didi/docker_test2/data_dir/mysql:/var/lib/mysql/ # 数据挂载，这次容器销毁，下次可以根据此目录读取
        - /home/didi/docker_test2/log_dir/mysql/:/var/log/mysql # 日志挂载
      environment: # 环境变量
        MYSQL_ROOT_PASSWORD: didi1234 # Root 密码
        
        MYSQL_USER: ych # 创建用户
        MYSQL_PASSWORD: didi1234
        
        # 也就是说，在创建的时候，root是可以进入的，但是创建出来的用户是没有权限的，是无法进入的
        # 因此需要手动给新创建的用户赋予权限（不一定创建了，进行mysql容器中手动创建）
  
  			# mysql> select User,Host,authentication_string from mysql.user; 查看mysql用户信息
      
  
  ```

- 注意：**需要手动给mysql新创建的用户赋予权限** [操作链接](https://blog.csdn.net/ych9527/article/details/119938198?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522165690019916781818737799%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fblog.%2522%257D&request_id=165690019916781818737799&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~blog~first_rank_ecpm_v1~rank_v31_ecpm-1-119938198-null-null.185^v2^control&utm_term=mysql&spm=1018.2226.3001.4450)

- 执行：docker-compose up -d 

# 二、整体启动文件

## 2.1 docker-compose 文件

```python
version: "3.9"

services:


  mysql:
    image: mysql:5.7 # 使用镜像
    container_name: map_mysql # 容器名称
    restart: always # 自动重启
    ports:
      - "3306:3306" # 端口映射
    networks:
      - default
    command: --default-authentication-plugin=mysql_native_password
    volumes: # 进行数据的挂载-> 前面的本地被挂载处：后面为容器中配置文件应处位置
      - ./mysql.conf:/etc/mysql/my.cnf
      - ${DATA_DIR}/mysql:/var/lib/mysql/
      - ${LOG_DIR}/mysql/:/var/log/mysql
    environment: # 环境变量
      MYSQL_ROOT_PASSWORD: didi1234
      MYSQL_USER: voyager # 启动的时候创建一个用户，需要注意的是这个用户没有任何权限，需要手动赋予
      MYSQL_PASSWORD: didi1234

  redis:
    image: redis:6.2
    container_name: map_redis
    restart: always
    ports:
      - "6379:6379"
    networks:
      - default
    command: redis-server /usr/local/etc/redis/redis.conf # 按照配置启动reids服务
    volumes:
      - ./redis.conf:/usr/local/etc/redis/redis.conf
      - ${DATA_DIR}/redis:/data/
      - ${LOG_DIR}/redis:/logs/

  mongo:
    image: mongo:4.4
    container_name: map_mongo
    restart: always
    command: --config /etc/mongo/mongod.conf
    ports:
      - "27017:27017"
    networks:
      - default
    volumes:
      - ./mongod.conf:/etc/mongo/mongod.conf
      - ${DATA_DIR}/mongodb:/data/db/
      - ${LOG_DIR}/mongodb:/var/log/mongodb
    environment:
      MONGO_INITDB_ROOT_USERNAME: root
      MONGO_INITDB_ROOT_PASSWORD: didi1234
```

## 2.2 配置文件

- 通过映射关系来使配置文件生效

```python
# ————————————————————————mysql____________
[client]
port            = 3306
socket          = /var/run/mysqld/mysqld.sock # mysql以socket方式运行的sock文件位置

[mysqld]
port = 3306 # 端口号

pid-file        = /var/run/mysqld/mysqld.pid # 进程id文件
socket          = /var/run/mysqld/mysqld.sock

datadir         = /var/lib/mysql # mysql的数据目录
log-error      = /var/log/mysql/error.log # 错误日志的位置



# By default we only accept connections from localhost
#bind-address   = 127.0.0.1


# Disabling symbolic-links is recommended to prevent assorted security risks
symbolic-links=0 # 是否支持符号链接，即数据库或表可以存储在my.cnf中指定datadir之外的分区或目录，为0不开启


# _____________redis__________________

# Accept connections on the specified port, default is 6379 (IANA #815344).
# If port 0 is specified Redis will not listen on a TCP socket.
port 6379

# 当客户端闲置多长时间后关闭连接，如果指定为0，表示关闭该功能
timeout 0

save 900 1 # 900s内至少一次写操作则执行bgsave进行RDB持久化
save 300 10
save 60 10000

# 指定存储至本地数据库时是否压缩数据，默认为yes，Redis采用LZF压缩，如果为了节省CPU时间，可以关闭该选项，但会导致数据库文件变的巨大
rdbcompression yes

# 指定本地数据库文件名，默认值为dump.rdb
dbfilename dump.rdb

# 工作目录.
# 指定本地数据库存放目录，文件名由上一个dbfilename配置项指定
#
# Also the Append Only File will be created inside this directo
#
# 注意，这里只能指定一个目录，不能指定文件名
dir /data

# By default protected mode is enabled. You should disable it only if
# you are sure you want clients from other hosts to connect to Redis
# even if no authentication is configured, nor a specific set of interfaces
# are explicitly listed using the "bind" directive.
protected-mode no

# 指定是否在每次更新操作后进行日志记录，Redis在默认情况下是异步的把数据写入磁盘，如果不开启，可能会在断电时导致一段时间内的数据丢失。
# 因为redis本身同步数据文件是按上面save条件来同步的，所以有的数据会在一段时间内只存在于内存中。默认为no
# IMPORTANT: Check the BGREWRITEAOF to check how to rewrite the append
# log file in background when it gets too big.
appendonly yes

# 指定更新日志文件名，默认为appendonly.aof
# appendfilename appendonly.aof

# 指定更新日志条件，共有3个可选值：
# no:表示等操作系统进行数据缓存同步到磁盘（快）
# always:表示每次更新操作后手动调用fsync()将数据写到磁盘（慢，安全）
# everysec:表示每秒同步一次（折衷，默认值）
appendfsync everysec

# 设置Redis连接密码，如果配置了连接密码，客户端在连接Redis时需要通过auth <password>命令提供密码，默认关闭
requirepass didi1234






#———————————————————————— mongod.conf————————————————

# for documentation of all options, see:
# http://docs.mongodb.org/manual/reference/configuration-options/

# where to write logging data.
systemLog:
  destination: file
  logAppend: true
  path: /var/log/mongodb/mongod.log

# network interfaces
net:
  port: 27017
  bindIp: 0.0.0.0

# Where and how to store data.
storage:
  dbPath: /data/db
  journal:
    enabled: true
  directoryPerDB: true
  engine: wiredTiger
  wiredTiger:
    engineConfig:
      directoryForIndexes: true

# how the process runs
processManagement:
  timeZoneInfo: /usr/share/zoneinfo

#security:

#operationProfiling:

#replication:

#sharding:

## Enterprise-Only Options:

#auditLog:

#snmp:

```



# 三、权限问题

- 在docker-compose 启动容器的时候，进行了目录的挂载

- 比如mysql启动容器就是mysql用户，但是在进行读取的时候是没有权限的，因此需要进行一个权限的设置

  ```
   #entrypoint: bash -c "chown -R mysql:mysql /var/log/mysql && exec /entrypoint.sh mysqld"
  ```

- [权限问题参考链接](https://github.com/docker-library/mysql/issues/688)



# 操作指令

- 容器启动失败
  - docker logs  {容器id} 查看容器启动日志
  - docker-compose up -d 批量后台启动
  - docker-compose ps 批量查看

# 其它问题

- 挂载目录出现目录错误[参考链接](https://blog.csdn.net/weixin_55554304/article/details/119733763?spm=1001.2101.3001.6661.1&utm_medium=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7ECTRLIST%7Edefault-1-119733763-blog-106680762.pc_relevant_aa&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7ECTRLIST%7Edefault-1-119733763-blog-106680762.pc_relevant_aa&utm_relevant_index=1)，修改my.cnf 在[mysqld]内加入secure_file_priv=/var/lib/mysql

- [环境变量的使用](https://blog.csdn.net/weixin_34279184/article/details/86021328?spm=1001.2101.3001.6650.1&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7Edefault-1-86021328-blog-122867820.pc_relevant_aa2&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7Edefault-1-86021328-blog-122867820.pc_relevant_aa2&utm_relevant_index=2)



# docker-compose 中的变量

- depends_on 
  - **依赖的服务**，比如A服务在启动的时候，B服务必须已经启动了，因此就可以在A 服务的depends_on 下面写上B服务
