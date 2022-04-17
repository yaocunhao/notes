# 一、基本操作

## 1.1 文件的打开方式

- 文件打开后必须关闭，因此给出了with....as 语法糖，运行完后会自动关闭文件

  ```python
  with open("file_name","op") as f: # f为返回的文件指针对象
    pass
  ```

## 1.2 读取方式

- read()

  -  一次性读取文件全部内容。如果文件内容很多，容易导致内存爆满，所以可以反复调用`f.read(size)`，每次最多读取size字节内容

    ```python
    import time
    def func():
      with open("json_test.txt", "r+", encoding="utf-8") as f:
        while True:
          ret = f.read(2)
          if len(ret) == 0: # 读取到了文件尾巴
            break
          print(ret)
    
    func()
    ```

- readline()

  - 每次读取一行内容

    ```python
    import time
    def func():
      with open("json_test.txt", "r+", encoding="utf-8") as f:
        while True:
          ret = f.readline() # 一行行读取
          if len(ret) == 0: # 读取到了文件尾巴
            break
          print(ret)
    
    func()
    ```

- readlines()

  - 一次读取所有内容放入到list之中然后进行返回

    ```python
    import time
    def func():
      with open("json_test.txt", "r+", encoding="utf-8") as f:
        ret = f.readlines() # 一次性全部读取
        print(type(ret),ret) # <class 'list'> ['123456\n', 'cccc\n', 'wssss']
    
    func()
    ```

    