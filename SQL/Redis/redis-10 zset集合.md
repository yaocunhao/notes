# 一、基本概念

## 1.1 定义

- 与 set 集合的相同之处在于，集合中的每一个成员都是字符串类型，并且不允许重复
- 与set集合不同之处在于，有序集合是有序的，set 是无序的，这是因为有序集合中每个成员都会关联一个 double（双精度浮点数）类型的 score (分数值)，Redis 正是通过 score 实现了对集合成员的排序
- 常用于排行榜类型，比如 QQ 音乐排行榜、用户贡献榜等。

## 1.2 底层结构

### 1.2.1 压缩列表

- 压缩列表由以下五个部分组成

  ![image-20220506221529177](https://gitee.com/yao-cunhao/ssh_picture/raw/master/pict/202205062215311.png)

  - zlbytes 是一个无符号整数，表示**当前 ziplist 占用的总字节数**
  - zltail 指的是压缩列表尾**部元素相对于压缩列表起始元素的偏移量**
  - zllen 指 ziplist 中 entry 的数量。当 zllen 比`2^16 - 2`大时，需要完全遍历 entry 列表来获取 entry 的总数目
  - entry 用来存放具体的数据项（score(分数) 和 member(成员)），长度不定，可以是字节数组或整数，entry 会根据成员的数量自动扩容
  - zlend 是一个单字节的特殊值，等于 255，起到标识 ziplist 内存结束点的作用

- 示例

  - 下面执行`ZADD`命令添加两个成员：xh（小红） 的工资是 3500.0；xm（小明） 的工资是 3200.0

    ```
    ZADD salary 3500.0 xh 3200.0 xm
    ```

  - 上述成员在压缩列表中的布局，如下所示：

  

  ![压缩列表结构](https://gitee.com/yao-cunhao/ssh_picture/raw/master/pict/202205062218331.gif)

  - 当 zset 使用压缩列表保存数据时，entry 的第一个节点保存 member，第二个节点保存 score。依次类推，**集合中的所有成员最终会按照 score 从小到大排列**

- 有序集合（zset）同样使用了两种不同的存储结构，分别是 zipList（压缩列表）和 skipList（跳跃列表），当 zset 满足以下条件时使用压缩列表

  - 成员的数量小于128 个

  - 每个 member （成员）的字符串长度都小于 64 个字

### 1.2.2 跳跃列表

- 当有序结合不满足使用压缩列表的条件时，就会使用 skipList 结构来存储数据
- 跳跃列表（skipList）又称“跳表”是一种基于链表实现的随机化数据结构，其插入、删除、查找的时间复杂度均为 O(logN)

# 二、zset 命令

## 2.1 基础命令

- 设置

  | zadd key score v | 用于将一个或多个成员添加到有序集合中，或者更新已存在成员的 score 值 |
  | ---------------- | ------------------------------------------------------------ |

- 查看

  | zrange key start stop 【索引(0,-1查看所有)】 | 返回有序集合中指定索引区间内的成员数量。                     |
  | -------------------------------------------- | ------------------------------------------------------------ |
  | zrangebylex key min max【成员】              | 返回有序集中指定字典区间内的**成员**。**分数必须相同**       |
  | zrangebyscore key min max 【分数】           | 返回有序集合中指定分数区间内的成员。                         |
  | zrevrange key start stop 【索引】            | 返回有序集中**指定区间**内的成员，通过索引，分数从高到低。   |
  | zrevrangebyscore key max min 【分数】        | 返回有序集中指定**分数区间**内的成员，分数从高到低排序。     |
  | zrevrank key member                          | 返回有序集合中指定成员的排名，有序集成员按分数值递减(从大到小)排序。 |

- 集合

  | zinterstore new_key zset_num key1,key2 | 求两个或者多个有序集合的交集，并将所得结果存储在新的 key 中。zset_num 表示有多少个集合 |
  | -------------------------------------- | ------------------------------------------------------------ |
  | zunionstore destination numkeys key    | 求两个或多个有序集合的并集，并将返回结果存储在新的 key 中。  |

- 删除

  | zrem key member                | 移除有序集合中的一个或多个成员。           |
  | ------------------------------ | ------------------------------------------ |
  | zremrangebyrank key start stop | 移除有序集合中指定**字典区间**的所有成员。 |
  | zremrangebyscore key min max   | 移除有序集合中指定**分数区间**内的所有成员 |

- 查看成员

  | zrank key member  | 返回有序集合中指定成员的排名。   |
  | ----------------- | -------------------------------- |
  | zscore key member | 返回有序集中，指定成员的分数值。 |

- 其它指令

  | zcard key                                      | 获取有序集合中成员的数量                                     |
  | ---------------------------------------------- | ------------------------------------------------------------ |
  | zcount key min max                             | 用于统计有序集合中指定 score 值范围内的元素个数。            |
  | zincrby key increment member                   | 用于增加有序集合中成员的分值。                               |
  | zlexcount key min max【min/max成员】           | 计算有序集合中在指定词典范围内的**成员的数量**。**分数必须相同** |
  | zscan key cursor [MATCH pattern] [COUNT count] | 迭代有序集合中的元素（包括元素成员和元素分值）。             |

## 2.2 '字典'命令讲解

- [参考链接](https://blog.csdn.net/succing/article/details/121081595?utm_medium=distribute.pc_relevant.none-task-blog-2~default~baidujs_utm_term~default-0-121081595-blog-103548791.pc_relevant_paycolumn_v3&spm=1001.2101.3001.4242.1&utm_relevant_index=3)

- zrangebylex key min max  
  - 返回有序集中指定字典区间内的成员
  - 和zlexcount 用法一样，只是该命令返回的是成员

- zlexcount key min max

  - **分数必须相同**! 如果有序集合中的成员分数有不一致的,返回的结果就不准
  - min、max 是成员名称

  - 成员名称前需要加 [ 符号作为开头, [ 符号与成员之间不能有空格

  - 可以使用 - 和 + 表示得分最小值和最大值

  - min 和 max 不能反, max 放前面 min放后面会导致返回结果为0

  - 计算成员之间的成员数量时,参数 min 和 max 的位置也计算在内。

  - min 和 max 参数的含义与 zrangebylex 命令中所描述的相同
    