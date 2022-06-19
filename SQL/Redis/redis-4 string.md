# 一、redis字符串

## 1.1 定义

- Redis 使用标准 C 语言编写，但在存储字符时，Redis 并未使用 C 语言的字符类型，而是自定义了一个属于特殊结构 SDS（Simple Dynamic String）即简单动态字符串），这是一个可以修改的内部结构

- 结构如下

  ```c
  struct sdshdr{
       //记录buf数组中已使用字符的数量，等于 SDS 保存字符串的长度
       int len;
    
       //记录 buf 数组中未使用的字符数量
       int free;
    
       //字符数组，用于保存字符串
       char buf[];
  ```

- 示意图![image-20220619231501086](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202206192315163.png)

  -  buf 数组存储的字符串仍然以 C语言字符格式的“\0”结尾的，这样做的目的是为了能够重用 C语言库 <string.h> 中的部分函数
  - 在 C语言中，字符串类型的结尾以空字符串 ‘\0’来标识的。但在某些情况下，字符串可能会包含具有实际意义的“空字符”，此时 C语言就无法正确的存取这个字符了，而 Redis 通过 len 来标识字符串的总长度，从而保证了数据的二进制安全特性	

## 1.2 空间分配

- 和C++中容器的设计理念一样，都会预先分配空间，在一定程度上提高了效率

## 1.3 扩容方式

- 当字符串所占空间小于 1MB 时，Redis 对字符串存储空间的扩容是以成倍的方式增加的
- 而当所占空间超过 1MB 时，每次扩容只增加 1MB。Redis 字符串允许的最大值字节数是 512 MB	

# 二、字符串命令

## 2.1 使用技巧

- 在输入命令的时候，命令行会有提示

  ```python
  127.0.0.1:6379> SET key value [EX seconds|PX milliseconds] [NX|XX]
  ```

  - 其中`[]`内代表可选参数，其含义如下所示
    - EX seconds：设置指定的过期时间，以秒为单位
    - PX milliseconds：设置指定的过期时间，以毫秒为单位
    - NX：先判断 key 是否存在，如果 key 不存在，则设置 key 与 value
    - XX：先判断 key 是否存在，如果 key 存在，则重新设置 value

## 2.2 常用命令

- 设置、读取

  | **set key value** | 用于设定指定键的值           |
  | ----------------- | ---------------------------- |
  | setnx key value   | 当 key 不存在时设置 key 的值 |
  | **get key value** | 用于检索指定键的值           |

- 批量设、批量读取

  | **mset key value  key value**  | 批量设置，只适用于key不存在时                       |
  | ------------------------------ | --------------------------------------------------- |
  | **msetnx key value key value** | 当指定的 key 都不存在时，用于设置多个键值对         |
  | **mget k1 k2**                 | 批量获取一个或多个 key 所存储的值，减少网络耗时开销 |

- 数值操作

  | incr key                   | 将 key 所存储的整数值加 1。                          |
  | -------------------------- | ---------------------------------------------------- |
  | decy key                   | 将 key 所存储的整数值减 1。                          |
  | incrby key increment       | 将 key 所储存的值加上给定的递增值（increment）。     |
  | decrby key decrement       | 将 key 所储存的值减去给定的递减值（decrement）。     |
  | incrbyfloat  key increment | 将 key 所储存的值加上指定的浮点递增值（increment）。 |
  | append key value           | 该命令将 value 追加到 key 所存储值的末尾。           |

- 截断操作

  | getrange key start end        | 截断key，返回一部分key的子串              |
  | ----------------------------- | ----------------------------------------- |
  | **setrange key offset value** | 从offset位置开始，用value覆盖原来的字符串 |

- 其它操作

  | getset key value              | 重新设置key，并且返回key之前对应的旧值                     |
  | ----------------------------- | ---------------------------------------------------------- |
  | getbit key offset             | 对 key 所存储的字符串值，获取其指定偏移量上的位（bit）。   |
  | setbit key offset value       | 对 key 所储存的字符串值，设置或清除指定偏移量上的位(bit)。 |
  | **setex key seconds value**   | 设置一个键值对的同时，设置过期时间                         |
  | strlen key                    | 返回 key 所储存的字符串值的长度。                          |
  | pseten key milliseconds value | 此命令用于设置 key 的值和有过期时间（以毫秒为单位）。      |



- 演示

  ```python
  # setex 设置值和设置过期时间
  127.0.0.1:6379> setex kkk 60 hello
  OK
  127.0.0.1:6379> get kkk
  "hello"
  127.0.0.1:6379> ttl kkk
  (integer) 34
  
  # getrange 截取k对应的值
  127.0.0.1:6379> set k abcdef
  OK
  127.0.0.1:6379> getrange k 0 3
  "abcd"
  
  # msetnx  同时设置多个值，但是key要求是新的
  127.0.0.1:6379> msetnx key_1 v1 key_2 v2
  (integer) 1
  
  # setrange key offset value 从offset位置开始，用value覆盖原来的字符串
  127.0.0.1:6379> set key abcdefgh
  OK
  127.0.0.1:6379> setrange key 2 123
  (integer) 8
  127.0.0.1:6379> get key
  "ab123fgh"
  ```

  