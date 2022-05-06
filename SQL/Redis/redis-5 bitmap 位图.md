# 一、基础概念

- 位图（bitmap）同样属于 string 数据类型。Redis 中一个字符串类型的值最多能存储 512 MB 的内容，每个字符串由多个字节组成，每个字节又由 8 个 Bit 位组成。位图结构正是使用“位”来实现存储的，它通过将比特位设置为 0 或 1来达到数据存取的目的，这大大增加了 value 存储数量，它存储上限为`2^32`

- 位图本质上就是一个普通的字节串，也就是 bytes 数组。您可以使用`getbit/setbit`命令来处理这个位数组，位图的结构如下所示：

  

  ![位数组](https://gitee.com/yao-cunhao/ssh_picture/raw/master/pict/1333395108-0.gif)

# 二、位图应用原理

- 应用场景
  - 统计签到数时，一天只需要一个比特位，一年也只需要365个，大约46个字节，统计总数只需要遍历统计1的个数即可
- Redis 的**位数组是自动扩展的**，如果设置了某个偏移位置超出了现有的内容范围，位数组就会自动扩充

- 验证

  - 获取 h 和 e 两个字符的比特位

    ```python
    >>> bin(ord("h")) # h的比特位
    '0b1101000'
    
    >>> bin(ord("e"))
    '0b1100101'
    ```

  - 如下图所示，两个字符组成的比特’数组‘的下标就叫做偏置位

    ![image-20220420000023162](https://gitee.com/yao-cunhao/ssh_picture/raw/master/pict/image-20220420000023162.png)

  ```python
  >>> bin(ord("h")) # h的比特位
  '0b1101000'
  >>> bin(ord("e"))
  '0b1100101'
  
  # 偏置位 1、2、4、9、10、13、15 对应的比特位都是1，下面进行设置
  127.0.0.1:6379> setbit bit_key 1 1
  (integer) 0
  127.0.0.1:6379> setbit bit_key 2 1
  (integer) 0
  127.0.0.1:6379> setbit bit_key 4 1
  (integer) 0
  
  # get 获取对应的值，如果不能打印则打印16进制
  127.0.0.1:6379> get bit_key
  "h"
  127.0.0.1:6379> setbit bit_key 9 1
  (integer) 0
  127.0.0.1:6379> setbit bit_key 10 1
  (integer) 0
  127.0.0.1:6379> setbit bit_key 13 1
  (integer) 0
  127.0.0.1:6379> setbit bit_key 15 1
  (integer) 0
  
  # 获取对应的值
  127.0.0.1:6379> get bit_key
  "he"
  
  # getbit 获取对应比特位的值
  127.0.0.1:6379> getbit bit_key 1
  (integer) 1
  
  # bitcount 统计区间1的个数
  127.0.0.1:6379> bitcount bit_key 0 15
  (integer) 7
  ```



# 三、常见命令

- setbit key offset value

  - 将对应偏置位上面的比特位设置为1

- getbit offser

  - 获取偏置位对应的比特值

- bitcount key [start, end]

  - 统计范围内1的个数

  - start和end与getrange 命令类似，可以使用负数，-1表示倒数第一个位

    ```python
    127.0.0.1:6379> bitcount bit_key -15 -1
    (integer) 7
    ```

    