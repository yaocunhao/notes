# 一、[OS](https://blog.csdn.net/weixin_41666747/article/details/108480362?spm=1001.2101.3001.6650.1&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1.pc_relevant_default&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1.pc_relevant_default)

-  `os.path.abspath('../../../../..')`

  - 返回一个目录的绝对路径

- `os.walk(dir_path)`

  - 目录遍历器，遍历指定的目录

    - 返回值 root, dirs, files
      - `root`保存的就是当前遍历的文件夹的**绝对路径**；
      - `dirs`保存当前文件夹下的所有子**文件夹**的名称（**仅一层，孙子文件夹不包括**）
      - `files`保存当前文件夹下的所有**文件**的名称

    ![image-20220329215927982](../../Library/Application Support/typora-user-images/image-20220329215927982.png)

- `os.path.splitext(file)`

  - 分离文件名和扩展名
  - 返回值为一个tuple

- os.path.exists(file)

  - 判断文件是否存在

- os.path.dirname(path)

  - 去掉文件名，返回目录

- os.path.join()

  - 函数功能：连接两个或更多的路径名组件

  - **在头尾都会加上/**(如果各组件名首字母不包含’/’，则函数会自动加上,如果最后一个组件为空，则生成的路径以一个’/’[分隔符](https://so.csdn.net/so/search?q=分隔符&spm=1001.2101.3001.7020)结尾)

  - 如果有一个组件是一个绝对路径，则在它之前的所有组件均会被舍弃

    ```python
    path1 = "Users/didi/PycharmProjects"
    path2 = "pythonProject"
    path3 = "/2312"
    
    out = os.path.join(path1, path2)
    print(out) # Users/didi/PycharmProjects/pythonProject
    
    #添加绝对路径，前面的将会被舍弃
    out = os.path.join(path1, path2,path3)
    print(out) #/2312
    ```

    

# 二、[subprocess](https://docs.python.org/2/library/subprocess.html?highlight=subprocess#module-subprocess)

## 2.1 作用

- 子进程管理

## 2.2 接口介绍

- 通常是传入两个参数，第一个为子进程执行的命令，第二个为shell
  - shell==false，这也是默认类型 ，argc接收的为list类型
  - shell==true，argc接收的为字符串类型

- subprocess.check_output

  - 可以执行一条sh命令(命令行命令)，并返回命令的输出内容,eg:

    - `output = subprocess.check_output(["python3", "xx.py"], shell = False)`

    - 注意**返回值不是字符串**,返回的值是一个编码后的比特值，实际编码格式取决于调用的命令，所以需要在应用层去解码

      ```python
      version = subprocess.check_output(["protoc --version"], shell=True)
      # 解码前
      print(version) #b'libprotoc 3.19.4\n'
      
      # 解码后
      version = version.decode('utf-8') #进行解码
      print(version) # libprotoc 3.19.4
      ```

- check_call
  - 与call方法类似，区别是命令执行失败的时候，check_call会抛异常

# 三、setup() 

[参考链接](https://blog.csdn.net/kuronekonano/article/details/114770201?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522164861097116780265450789%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=164861097116780265450789&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-1-114770201.142^v5^pc_search_result_control_group,143^v6^control&utm_term=python+setup%28%29&spm=1018.2226.3001.4187)

- 打包分发最关键的一步是编写setup

# 四、[shapely](https://shapely.readthedocs.io/en/maint-1.8/manual.html#introduction)

- StringLine 不闭合的折线
- StringRing 闭合的折线
- xy
  - StringLine
    - `(minx, miny, maxx, maxy)` 元组
    - xy[0] 获取x坐标
    - xy[1] 获取y坐标

# 五、shutil

- 文件操作

- [参考链接](https://blog.csdn.net/qq_40223983/article/details/95984230)

# 六、shlex

- 按照shell 的方式进行文件字符串解析

- [参考链接](https://blog.csdn.net/weixin_34034261/article/details/85908583?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522164964804316780274134727%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=164964804316780274134727&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-2-85908583.142^v7^control,157^v4^control&utm_term=shlex&spm=1018.2226.3001.4187)

  ```python
  import shlex
  command = "poython   manage.py 0.0.0.0:6001 "
  command = shlex.split(command)
  print(command) # ['poython', 'manage.py', '0.0.0.0:6001']
  
  ```

  