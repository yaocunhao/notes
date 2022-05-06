- Redis 没有提供新建数据库的操作，因为它自带了 16 （0—15）个数据库（默认使用 0 库）。在同一个库中，key 是唯一存在的、不允许重复的，它就像一把“密钥”，只能打开一把“锁”。键值存储的本质就是使用 key 来标识 value，当想要检索 value 时，必须使用与 value 相对应的 key 进行查找
- Redis 数据库**没有“表”的概念**，它通过不同的数据类型来实现存储数据的需求，不同的数据类型能够适应不同的应用场景

- Redis 基于内存来实现数据的存储，因此其速度非常快。但是我们知道，计算机的内存是非常珍贵的资源，所以 Redis 不适合存储较大的文件或者二进制数据，否则会出现错误，Redis 适合存储较小的文本信息。理论上 Redis 的每个 key、value 的大小不超过 512 MB



## 启动redis客户端

- redis-cli

## 网页版redis

- [测试联系使用](https://try.redis.io/)

##  Redis 可视化工具

- https://github.com/uglide/RedisDesktopManager