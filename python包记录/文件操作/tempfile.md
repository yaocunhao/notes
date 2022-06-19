# 一、简介

- tempfile 模块专门用于创建**临时文件和临时目录**

# 二、常用函数

| **tempfile 模块函数**                                        | **功能描述**                                                 |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| tempfile.TemporaryFile(mode=‘w+b’, buffering=None, encoding=None, newline=None, suffix=None, prefix=None, dir=None) | 创建临时文件。该函数返回一个类文件对象，也就是支持文件 I/O   |
| tempfile.NamedTemporaryFile(mode=‘w+b’, buffering=None, encoding=None, newline=None, suffix=None, prefix=None, dir=None, delete=True) | 创建临时文件。该函数的功能与上一个函数的功能大致相同，只是它生成的临时文件在文件系统中有文件名 |
| tempfile.SpooledTemporaryFile(max_size=0, mode=‘w+b’, buffering=None, encoding=None, newline=None, suffix=None, prefix=None, dir=None) | 创建临时文件。与 TemporaryFile 函数相比，当程序向该临时文件输出数据时，会先输出到内存中，**直到超过 max_size 才会真正输出到物理磁盘中** |
| tempfile.TemporaryDirectory(suffix=None, prefix=None, dir=None) | 生成临时目录                                                 |
| tempfile.gettempdir()                                        | 获取系统的临时目录                                           |
| tempfile.gettempdirb()                                       | 与 gettempdir() 相同，只是该函数返回字节串                   |
| tempfile.gettempprefix()                                     | 返回用于生成临时文件的前缀名                                 |
| tempfile.gettempprefixb()                                    | 与 gettempprefix() 相同，只是该函数返回字节串                |

- tempfile 模块还提供了 tempfile.mkstemp() 和tempfile.mkdtemp()两个低级别的函数。上面介绍的 4 个用于创建临时文件和临时目录的函数都是高级别的函数，高级别的函数支持自动清理，而且可以与 with 语句一起使用，而这两个低级别的函数则不支持，因此一般推荐使用高级别的函数来创建临时文件和临时目录

- 需要注意的是，不要去找临时文件或临时文件夹， 因为**程序退出时该临时文件和临时文件夹都会被删除。**