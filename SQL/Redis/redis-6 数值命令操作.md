# 一、概念

- redis string 类型提供了一些专门操作**数值**的命令(加减等操作)。比如常见的点赞，关注这类操作就可以通过数值命令来进行操作
- 此时key对应的必须是一个整数或者浮点数，因为是对其进行自增或者自减操作。这个数值占64个比特位，因此范围是-9223372036854775808 至 9223372036854775807，超过这个范围redis就会报错

# 二、常见命令

## 2.1 incr 

- 对value数值加1操作，如果key不存在，则创建一个key，并且将value初始化为1

  ```python
  127.0.0.1:6379> incr i_key
  (integer) 1
  127.0.0.1:6379> get i_key
  "1"
  127.0.0.1:6379> incr i_key
  (integer) 2
  127.0.0.1:6379> get i_key
  "2"
  127.0.0.1:6379> incr i_key
  (integer) 3
  127.0.0.1:6379> get i_key
  "3"
  ```

## 2.2 decr

- 与incr相反

  ```python
  127.0.0.1:6379> decr i_key
  (integer) 2
  ```

## 2.3 incrby 

- 在原来的数值基础上指定数值进行自增运算

  ```python
  127.0.0.1:6379> incrby i_key 100
  (integer) 102
  ```

## 2.4 decrby

- 在原来的数值基础上，指定值进行自减运算

  ```python
  127.0.0.1:6379> decrby i_key 100
  (integer) 2
  ```

## 2.5 incrbyefloat

- string中唯一操作浮点数的命令，增/减由符号判断

  ```python
  127.0.0.1:6379> incrbyfloat i_key 100
  "102"
  127.0.0.1:6379> incrbyfloat i_key -100.2121
  "1.7879"
  ```

  