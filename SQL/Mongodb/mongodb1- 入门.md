# 一、服务的启动和停止

[启动参考链接](https://www.cnblogs.com/runningRain/p/13809109.html)

- 启动MongoDB

  ```python
  mongod --dbpath /var/lib/mongodb --logpath /var/log/mongodb/mongod.log --fork
  ```

- 停止MongoDB

  ```python
  mongod --dbpath /var/lib/mongodb --logpath /var/log/mongodb/mongod.log --shutdown
  ```

- 进入交互shell

  ```
  mongo
  ```

# 二、基础命令

- **"show dbs"** 命令可以显示所有数据的列表

- use 选择使用的数据库
- db 显示当前使用的数据库对象或者结合

# 三、操作

- 配置文件地址 `./etc/mongodb.conf`
- 远程连接的方式 ` mongo 10.190.9.216:27017`
- 文件连接 `mongod -f /etc/mongodb.conf  `







# mongodb 概念认识

## 数据库

- mongo 进行mongodb shell命令行

- show dbs 展示数据库

  ```python
  > show dbs
  admin   0.000GB
  config  0.000GB
  local   0.000GB
  test    0.000GB
  ```

  

## 集合

- 集合就是一组 MongoDB 文档的组合，**类似于关系型数据库（例如 MySQL）中的数据表**。集合存在于数据库中，且没有固定的结构，您**可以向集合中插入不同格式或类型的数据**

## 文档

- 文档是 **MongoDB 中数据的基本单位**，由 BSON 格式（一种计算机数据交换格式，类似于 JSON）的键/值对组成，类似于关系型数据库中的一行行数据，但要相对复杂一些

- 文档具有动态模式，所谓动态模式就是**同一集合中的文档不需要具有相同的字段**，即使是相同的字段也可以是不同的类型，这与关系型数据库有很大的区别，也是 MongoDB 最突出的特点之一

- 下表列举了关系型数据库与 MongoDB 中的一些差异

  | 关系型数据库 | MongoDB     | 解释说明                                  |
  | ------------ | ----------- | ----------------------------------------- |
  | database     | database    | 数据库                                    |
  | table        | collection  | 数据表/集合                               |
  | row          | document    | 数据行/文档                               |
  | column       | field       | 字段/域                                   |
  | index        | index       | 索引                                      |
  | table joins  |             | 表连接，MongoDB 中不支持                  |
  | primary key  | primary key | 主键，MongoDB 会自动将 _id 字段设置为主键 |

- 下面的示例中展示了一个简单的文档结构：

  ```python
  {
    _id: ObjectId(601e288aaa203cc89f2d31a7), # 12字节的十六进制数据，可以保证每个文档的唯一性
    title: 'MongoDB Concept',
    description: 'MongoDB is no sql database',
    by: '编程帮',
    url: 'http://www.biancheng.com',
    tags: ['mongodb', 'database', 'NoSQL'],
    likes: 100,
    comments: [
      {
       user:'user1',
       message: 'My first comment',
       dateCreated: new Date(2011,1,20,2,15),
       like: 0
      },
      {
       user:'user2',
       message: 'My second comments',
       dateCreated: new Date(2011,1,25,7,45),
       like: 5
      }
    ]
  }
  ```

  