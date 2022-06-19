- 普通推导模式

  ```python
  # 普通模式
  print([i for i in range(5)])
  # [0, 1, 2, 3, 4]
  ```

- 筛选模式

  ```python
  # 筛选模式
  print([i for i in range(5) if i > 2])
  print([i if i > 2 else "0000" for i in range(5)])
  # ['0000', '0000', '0000', 3, 4]
  ```

- 三元模式

  ```python
  # 三元模式
  print([i if i > 3 else "000" for i in range(5) if i > 2])
  # ['000', 4]
  ```

  ![image-20220405164942363](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202206192309152.png)

- for 循环推导模式

  - 先执行for循环

    ```python
    print([i for i in range(3) for i in range(2)])
    # [0, 1, 0, 1, 0, 1]
    ```

- 集合推导式

  - 注意天然去重

  ```python
  print({i for i in range(5)})
  # {0, 1, 2, 3, 4}
  ```

- 字典推导式

  ```python
  print({i:i+1 for i in range(5)})
  # {0: 1, 1: 2, 2: 3, 3: 4, 4: 5}
  ```

- 元组没有推导式

  ```python
  print((i+1 for i in range(5)))
  
  # 生成器推导式
  # <generator object <genexpr> at 0x7f9ae8efe990>
  ```

- 生成器推导式

  - ()不是元组推导式而是生成器推导式

  ```python
  g = ((i+1 for i in range(5)))
  print(next(g))
  print(next(g))
  print(next(g))
  print(next(g))
  print(next(g))
  
  # 1
  # 2
  # 3
  # 4
  # 5
  ```

  