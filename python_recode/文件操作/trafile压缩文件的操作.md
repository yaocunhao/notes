# 零、 背景

- 什么是tar
  - inux下最常用的打包程序就是tar了，**使用tar程序打出来的包我们常称为tar包**，tar包文件的命令通常都是以.tar结尾的
  - 生成tar包后，就可以用**其它的程序来进行压缩了**
  - **简单来说，tar是一个归档的动作，即将需要打包的文件进行归档，压缩和解压要使用其它的程序来进行**

# 一、作用

- Python标准库的tarfile模块中的函数可帮助创建tar存档并根据需要从tarball中提取。可以使用gzip，bz2和lzma压缩或完全不压缩来构建档案
- [官方文档](https://docs.python.org/3/library/tarfile.html)
- [参考链接](https://blog.csdn.net/qq_42817803/article/details/125290621?ops_request_misc=&request_id=&biz_id=102&utm_term=tarfile%20%E6%89%93%E5%BC%80%E6%96%87%E4%BB%B6%E7%9A%84%E6%96%B9%E5%BC%8F%EF%BC%8C%E5%8C%BA%E5%88%AB&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-1-125290621.142^v39^pc_rank_34_2&spm=1018.2226.3001.4187)

# 二、使用方法

## 2.1 打开方法

- ``tarfile.``open`(*name=None*, *mode='r'*, *fileobj=None*, *bufsize=10240*, ***kwargs*)`

- `trafile.open(file_name,mode)`此函数返回与文件名相对应的TarFile对象，该文件名作为参数提供给它
- 该函数需要另一个名为mode的参数
  - 需要注意的是， `r` 和 `r:`不同
  
    - `r等价r:*` **也就是匹配任意的读取方式**
    - `r:` 表示只能读取未压缩的
  
  - | 打开方式 | 功能                                                         |
    | -------- | ------------------------------------------------------------ |
    | r        | 打开以透明压缩方式阅读,**表示适配任何模式**                  |
    | r:gz     | 打开以使用gzip压缩进行阅读                                   |
    | r:bz2    | 打开以使用bzip2压缩进行读取                                  |
    | r:xz     | 以lzma压缩打开以供阅读                                       |
    | x        | 专门创建不压缩的tarfile                                      |
    | s:gz     | 用gzip压缩创建一个tarfile                                    |
    | x:bz2    | 创建具有bzip2压缩的tarfile                                   |
    | x:xz     | 创建具有lzma压缩的tarfile                                    |
    | a        | 打开以进行不压缩的追加                                       |
    | w        | 打开以进行未压缩的写入(**也就是进行归档**)                   |
    | w:gz     | 打开以进行gzip压缩编写                                       |
    | w:bz2    | 打开以进行bzip2压缩写入                                      |
    | w:xz     | 为lzma压缩写打开。该模块定义了TarFile类。代替open()函数，可以通过调用构造函数实例化TarFile对象 |

## 2.2 使用方法

### 2.2.1 操作tar 包

- **注意！！操作的前提是这个文件是一个tar包**

- 模式为`r`、`w`、`a`、`x`四种为主，外加使用add方法写入文件，退出依然使用close方法

- add(name, arcname)

  - add(压缩文件，**压缩别名**)

  - 注意，压缩别名一定不要以路径分隔符为结尾，否则只会创建一个文件夹

    ```python
    # 下面演示的是归档
    
    # 比如平常的open是打开一个文件，然后往文件之中写入内容
    # 这里的open是打开一个tar压缩包，然后往包里面写入文件
    
    import tarfile
    
    with tarfile.open('test.tar', 'w') as tf: # 打开tar包
        tf.add('test.txt') # 对包进行操作，这里是添加文件(归档操作)
    ```

### 2.2.2 压缩

- 压缩的方式主要是将模式改变，在`rwx`的基础上加上各个压缩的方式，变成：`r:gz`、`w:bz2`、`x:xz`等样式，然后在将刚才创建的tar包压缩，**当然也可以直接将文件压缩成为压缩包**

- 注意，`a`模式不能配备任何压缩模式，因为gzip、bzip2、xzip不能直接往里面追加文件，所以如果要追加文件 ，将tar包解压缩出来，然后追加再压缩

  ```python
  import tarfile
  
  # tar包以gzip格式压缩
  with tarfile.open('test.tar.gz', 'w:gz') as tf:
      tf.add('test.tar')
  ```

### 2.2.3 解压缩

- tarfile的解压缩方法和zipfile的一模一样。

  | 方法       | 含义                                                         | 参数                          |
  | ---------- | ------------------------------------------------------------ | ----------------------------- |
  | extract    | 解压缩指定文件,压缩包很大的情况下，内存不够用，则采用这种方式来解压文件 | member 指定解压出来的文件名称 |
  | extractall | 解压缩所有文件                                               | path(解压路径，默认为 . )     |

- getnames  返回压缩包中的文件(list)

  ```python
  def descompression():
    with tarfile.open('test.tar') as f: # 打开压缩包，里面有两个文件
      
      file_list = f.getnames()
      print(file_list) # ['note.txt', 'code.txt']
      
      f.extract('code.txt') # 将其中一个文件解压出来,注意会解压到当前目录的工作目录之下
  ```

### 2.2.4  删除压缩包之中的文件

- zipfile和tarfile不支持删除压缩包中的文件，所以如果有需要删除压缩包中的文件，可以将压缩包解压出来，然后删除其中的文件，再压缩



# 三、疑惑点记录

## 3.1 压缩的步骤

- 压缩可以先对文件进行tar归档，然后再进行压缩。**也可以不进行归档，直接进行压缩**

## 3.2 压缩完的文件名包含递归的路径怎么解决

- add(压缩文件，**压缩别名**)，进行压缩别名的设置即可

  ```python
  # 压缩
  with tarfile.open(zip_path, 'w:gz') as f:
    f.add(os.path.join(tmp_dir, '1.txt'),arcname='1.txt')
    f.add(os.path.join(tmp_dir, '2.txt'),arcname='2.txt')
  ```

  
