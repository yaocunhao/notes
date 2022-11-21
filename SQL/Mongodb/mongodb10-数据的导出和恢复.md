# 一、数据的备份

- 语法

  - `mongodump -h dbhost -p port -d dbname -o dbdirectory`

  - -h 主机

  - -p 端口

  - -d 数据库名称

  - -o 将备份的产出物放到那个目录之下

  - 和mongodump 一起使用的参数

    | 语法                                              | 描述                                 | 实例                                             |
    | ------------------------------------------------- | ------------------------------------ | ------------------------------------------------ |
    | mongodump --host HOST_NAME --port PORT_NUMBER     | 该命令将备份所有 MongoDB 数据        | mongodump --host runoob.com --port 27017         |
    | mongodump --dbpath DB_PATH --out BACKUP_DIRECTORY | 该命令将仅备份指定路径上的指定数据库 | mongodump --dbpath /data/db/ --out /data/backup/ |
    | mongodump --collection COLLECTION --db DB_NAME    | 该命令将备份指定数据库的集合         | mongodump --collection mycol --db test           |

- 演示

  ```shell
  # 加了验证条件
  # 可以通过mongo --help 来查看选项的作用
  mongodump -h ip:port -u user -p passwd --authenticationDatabase admin -d my_mongo -o ./
  ```

  

# 二 、数据的恢复

- 使用 `mongorestore --help` 查看命令的选项含义

  - -d 数据库名称
  - -c 表名称
  - --dir 备份时的数据存储的位置

  ```shell
   mongorestore -h ip:port -u user -p port --authenticationDatabase admin -d=my_mongo -c=mt --dir=my_mongo/mt.bson
  ```

  