# 一、文件路径操作(os模块)

- [参考链接](https://blog.csdn.net/kdongyi/article/details/82313150?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522164964763616780366553189%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=164964763616780366553189&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_positive~default-1-82313150.142^v7^control,157^v4^control&utm_term=os.path&spm=1018.2226.3001.4187)

- 实例

  ```python
  import os
  
  TRAIL_DIR = os.path.abspath(
      os.path.join(os.path.dirname(__file__), '../../'))
  
  r1 = os.path.dirname(__file__) # 获取当前的路径 /Users/didi/PycharmProjects/pythonProject
  r2 = os.path.join(r1,"../../") # 进行路径合成   /Users/didi/PycharmProjects/pythonProject/../../
  r3 = os.path.abspath(r2) # 返回绝对路径
  
  os.path.getsize('file_name'):判断文件的大小
  os.remove('file_name'): 删除文件
  os.listdir('dir_path')：返回指定目录路径中存在的文件和目录的列表。
  os.walk('dir_path')：递归获取目录和子目录中所有文件的列表。
  os.scandir('path')：返回目录条目以及文件属性信息。
  glob.glob('pattern'): glob 模块列出名称遵循特定模式的文件和文件夹。
  os.path.exists()：判断当前文件是否存在
  
  ```

- [pathlib 模块](https://blog.csdn.net/looker53/article/details/106064166?spm=1001.2101.3001.6661.1&utm_medium=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1.pc_relevant_aa&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1.pc_relevant_aa&utm_relevant_index=1)
  
  - 新的方法模块，比OS模块更加的合理

# 二、文件内容操作(shutil模块)

- 对文件内容进行操作
- [参考链接](https://blog.csdn.net/qq_40223983/article/details/95984230)

# 三、文件压缩操作(tarfile)

- [参考链接](https://blog.csdn.net/zhongbeida_xue/article/details/71124568?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522165456983416782390585819%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=165456983416782390585819&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-2-71124568-null-null.142^v11^pc_search_result_control_group,157^v13^control&utm_term=tarfile.open&spm=1018.2226.3001.4187)

# 四、创建临时文件

- [参考链接](https://blog.csdn.net/qq_32617703/article/details/101267426?ops_request_misc=%25257B%252522request%25255Fid%252522%25253A%252522165468941016780366524472%252522%25252C%252522scm%252522%25253A%25252220140713.130102334..%252522%25257D&request_id=165468941016780366524472&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-2-101267426-null-null.142%5Ev11%5Epc_search_result_control_group,157%5Ev13%5Econtrol&utm_term=+tempfile.TemporaryDirectory&spm=1018.2226.3001.4187)

# 五、文件的读取

- `mode='r'`表示只读；`mode='w'`表示只写；`mode='a'`表示追加。`mode='r+'`表示可读写，但是文件必须存在，否则报错
- 我们先来看看python读取文件后，获取文件内容的几种方法
  - f.read()	读取文件中所有内容
  - f.readline()	读取第一行的内容
  - f.readlines()	读取文件里面所有内容，把每行的内容放到一个list里面
    - 从上面三种方法不难看出，第一种和第三种都会一次性读取完文件，这样文件如果足够大，**都会将内存占满，如果要想解决上面的内存溢出问题**，可以使用第一种方法然后设置读取文件内容的大小
    - 其实第二种方法还有补充说明，就是读取第一行的内容后，光标会移动到第一行末尾，下次调用该方法时，读取第二行内容，依次类推

- 读取文件的时候，**是先将内容加载到内存的，因此需要设置读取文件的大小**

  ```python
  def file_check():
    """使用MD5对文件进行校验"""
    file_path = '/Users/didi/vimrc'
    block_size = 1 # 每次读取一个字节
    md5 = hashlib.md5()
    with open(file_path, 'r') as f:
      while True:
        data = f.read(block_size)
        print(data)
        if not data:
          break
        md5.update(data.encode('utf-8'))
    print(md5.hexdigest())
  ```

  
