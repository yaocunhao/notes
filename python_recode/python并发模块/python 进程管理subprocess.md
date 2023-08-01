# 一、sunbprocess

- subprocess 模块允许我们启动一个新进程，并连接到它们的输入/输出/错误管道，从而获取返回值
- subprocess 模块首先推荐使用的是它的 run 方法，更高级的用法可以直接使用 Popen 接口
- [语法参考链接](https://www.runoob.com/w3cnote/python3-subprocess.html)
- [实际操作参考链接](%2522request%255Fid%2522%253A%2522165399995216781483787096%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=165399995216781483787096&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-1-99435066-null-null.142^v11^pc_search_result_control_group,157^v12^control&utm_term=subprocess.run参数&spm=1018.2226.3001.4187)

# 二、subprocess.run

- 语法格式

  ```python
  subprocess.run(args, *, stdin=None, input=None, stdout=None, stderr=None, capture_output=False, shell=False, cwd=None, timeout=None, check=False, encoding=None, errors=None, text=None, env=None, universal_newlines=None)
  ```

  - args：表示要执行的命令。必须是一个字符串，字符串参数列表
  
  - stdin、stdout 和 stderr：子进程的标准输入、输出和错误
  
    - 其值可以是 subprocess.PIPE、subprocess.DEVNULL、一个已经存在的文件描述符、已经打开的文件对象或者 None
    - subprocess.PIPE 表示为子进程创建新的管道
    - subprocess.DEVNULL 表示使用 os.devnull
    - 默认使用的是 None，表示什么都不做
    - stderr 可以合并到 stdout 里一起输出
  
  - timeout：设置命令超时时间。如果命令执行时间超时，子进程将被杀死，并弹出 TimeoutExpired 异常
  
  - check：**如果该参数设置为 True，并且进程退出状态码不是 0，则弹 出 CalledProcessError 异常**
  
  - encoding: 如果指定了该参数，则 stdin、stdout 和 stderr 可以接收字符串数据，并以该编码方式编码。否则只接收 bytes 类型的数据。
  
  - shell：如果该参数为 True，将通过操作系统的 shell 执行指定的命令，并且可以执行复杂的shell命令
  
  - cwd：用于设置子进程的当前目录
  
  - capture_output=True：` 实质上会设置两个参数值 `stdout=subprocess.PIPE,`stderr=subprocess.PIPE` ，也就是它同时也会捕获标准错误和标准输出放置到管道之中，**也就是不会直接打印出来**
  
  - text：打印的格式，将bytes类型变为str类型
  
  - 默认时 `ret.stdout` 是一个 `bytes` 类型，要显示为文本需要用 `ret.stdout.decode()` ，或者再加上参数 `text=True` 就无需 `stdout.decode()` 了。用 `encoding="utf-8"` 也能免除 `decode()`
  
  - 输出重定向(文件或设备)
  
    ```python
    # 输入到文件output.txt 之中
    with open('output.txt', 'w') as file:
        subprocess.run(['ls', '-la'], stdout=file, text=True)
    ```
  
  - 返回值
    - **ret.returncode=0，则表示执行成功**
    - ret.stdout 标准输出
      - 执行命令的所有输出内容，会写到此处,**如果在传入的参数中该值是管道的话**
    - ret.stderr 标准错误
      - 执行命令出现错误，可以通过打印此处进行输出
  
- 演示

  ```python
  “”“ 通过该程序，在当前目录下创建一个文件夹””“
  
  import subprocess
  def runcmd(command):
      ret = subprocess.run(command,shell=True,stdout=subprocess.PIPE,stderr=subprocess.PIPE,encoding="utf-8",timeout=1)
      # command 执行的命令
      # shell 通过操作系统shell执行该命令
      # Pipe 为子进程创建新的管道，如果是管道，会将值写入到此处，否则直接输出，不会保存下来
      # utf-8 编码
      # 超时设置
  
      if ret.returncode == 0: # 状态码
          print("success:",ret,ret.stdout,ret.stderr)
      else:
          print("error:",ret,ret.stdout,ret.stderr)
  
  
  #序列参数， 由于是shell命令，因此shell=True
  # 如果shell为false，则将参数分隔开来即可 runcmd(["mkdir", "./test_dir"])
  runcmd(["mkdir ./test_dir"])
  ```
  
  

- **注意**
  - 通常用run启动子进程来执行一个脚本，需要给其传入执行的cmd命令，和cwd目录，也就是子进程工作的目录。**这里设置的cwd一定要是项目的工作目录，cmd命令也就是在项目工作目录下启动该脚本的命令**
