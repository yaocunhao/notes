# 一、分类

- 在python3中按数据类型的可变与不可变大致分为如下几种类型
  - **不可变数据（3 个）：**Number（数字）、String（字符串）、Tuple（元组）
  - **可变数据（3 个）：**List（列表）、Dictionary（字典）、Set（集合)

# 二、set

## 2.1 基本介绍

- python中，用set来表示一个无序不重复元素的序列。set的只要作用就是用来给数据去重
- 可以使用大括号 { } 或者 set() 函数创建集合，但是注意如果创建一个空集合必须用 set() 而不是 { }，因为{}是用来表示空字典类型的



## 二、使用特性

- 集合的创建
  - 使用set()函数创建集合
  - 只能传入一个参数，可以是list,tuple等 类型

- 自动去重

  - 对字符串也会自动去重

    ```python
    	s = set("aaaaa")
    	print(s) # {'a'}
    ```

- set `-`求差集，不能使用`+`

- 求合集使用update

  ```python
  set1 = [1] 
  set2 = [2]
  set2.update(set1)
  ```

  

- add添加元素

  - 可以是元组
  - 不可以是list、dict

- update添加元素

  - 可以是元组、list、dict
  - 不可以是字符串，会进行拆分

- remove删除元素

  - 不存在会报错
  - discard 功能一样，不存在不会报错

- 元素更新

  - 一般线remove再add

- 判断是否存在

  - 使用in判断

    ```python
    #查询是否存在，无法返回索引，使用in判断
    if "teacher" in person:
        print("true")
    else:
    		print("false")
    ```

- 清空set

  - clear