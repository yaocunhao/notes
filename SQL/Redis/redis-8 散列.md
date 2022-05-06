# 一、基本概念

## 1.1定义

- hash 是有字符类型的字段和value组成的哈希映射表结构(也称之为散列表)，非常类似表格结构

- 在hash类型之中，field和value一一对应，**不允许重复**

- redis hash 非常适合存储对象

  - 一个field/value可以看做表格中的一条记录

  - 一个key可以对应多条记录，**相当于一个key可以存储一个对象的所有变量**

    ![image-20220506194745511](https://gitee.com/yao-cunhao/ssh_picture/raw/master/pict/202205061947601.png)

    ```python
    # 以 id+序号为 key、name+名称为value
    127.0.0.1:6379> hmset hk id:1 name:zhangsan id:2 name:lisi
    
    # 相当于通过 hk这个键值 获取所有的key:value 
    127.0.0.1:6379> hgetall hk
    
    # key -> id:1,value -> name:zhangsan
    1) "id:1"						
    2) "name:zhangsan"
    
    # key->id:2, value->name:lisi 
    3) "id:2"
    4) "name:lisi"
    ```

- 对value进行查询时，返回的值为**字符串类型**

- 一个 hash 类型的 key 最多可以存储 2^32-1（约 40 亿个）字段/值。同时 Redis hash 会为这个 key 额外储存一些附加的管理信息，比如这个键的类型、最后一次访问这个键的时间等，所以 hash 键越来越多时，Redis 耗费在管理信息方面的内存就越多。当 hash 类型移除最后一个元素后，该存储结构就会被自动删除，其占用内存也会被系统回收

## 1.2 底层结构

- hash 类型是 Redis 常用数据类型之一，其底层存储结构有两种实现方式

### 1.2.1 ziplist

- 当存储的数据量较少的时，hash 采用 ziplist 作为底层存储结构，此时要求符合以下两个条件
  - 哈希对象保存的所有键值对（键和值）的字符串长度总和小于 64 个字节
  - 哈希对象保存的键值对数量要小于 512 个

### 1.2.2 dict(字典结构)

- 当无法满足ziplist结构的两个条件时，就会采用dict结构
- 在redis之中，dict是基于哈希表算法实现的，查询时间复杂度为O(1)

# 二、hash 命令

## 2.1 基础命令

- 设置值

  | hmset key field1 value1[field2 value2] | 在哈希表中同时设置多个 field-value(字段-值）                 |
  | -------------------------------------- | ------------------------------------------------------------ |
  | hset key field value                   | 用于设置指定 key 的哈希表字段和值（field/value），**重复的则覆盖** |
  | hsetnx key field value                 | 仅当字段 field 不存在时，设置哈希表字段的值                  |

- 获取值

  | hget key field                | 获取 key 关联的哈希字段的值。                   |
  | ----------------------------- | ----------------------------------------------- |
  | hgetall key                   | 获取 key 关联的所有哈希字段和值。               |
  | **hmget** key field1 [field2] | 用于同时获取多个给定哈希字段（field）对应的值。 |
  | hvals key                     | 用于获取哈希表中的所有值(不显示field)           |
  | hkeys key                     | 获取key关联的所有字段的值                       |
  | hlen key                      | 获取key关联的哈希表中字段的数量                 |

- 其它操作

  | hdel key field...field2          | 用于删除一个或多个哈希表字段。                        |
  | -------------------------------- | ----------------------------------------------------- |
  | hexists key field                | 用于确定哈希表字段是否存在。                          |
  | hincrby key field increment      | 给 key 关联的哈希字段做整数增量运算 。                |
  | hincrbyfloat key field increment | 给 key 关联的哈希字段做浮点数增量运算 。              |
  | hscan key cursor                 | 迭代哈希表中的所有键值对，cursor 表示游标，默认为 0。 |

