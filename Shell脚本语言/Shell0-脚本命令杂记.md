- set -ex

  - 通常写在脚本的前面

  - 这里其实是两个参数 set -e ,set -x

  - -e 当程序出现异常的时候停止运行

  - -x 在执行某一行命令前将其打印出来

    ```bash
    #!/bin/bash
    set -ex
    printf "first line\n"
    printf "second line\n"
    
    
    + printf 'first line\n' # 将命令打印出来
    first line
    + printf 'second line\n'
    second line
    ```

    

- 导入环境变量

  - sh脚本本质是启动一个shell子进程，如果直接在脚本中进行环境变量的导入，实际上是只对当前子shell进行了导入，并没有对父进程的环境变量进行设置

  ```bash
  # 不直接导入的原因是，启动脚本创建一个子进程，改变的是它自己的变量
  echo "export DATA_DIR=/home/didi/docker2/data_dir" >> ~/.bashrc
  ```


- getopts
  - 格式 `getopts optstring name [args]`
  - [参考链接](https://www.iteye.com/blog/xingwang-ye-1601246)
  - [参考链接](https://blog.csdn.net/weixin_42552315/article/details/111945203?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522166377715316782391837706%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fall.%2522%257D&request_id=166377715316782391837706&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~pc_rank_34-1-111945203-null-null.142^v49^pc_rank_34_1,201^v3^control_1&utm_term=%E4%BB%80%E4%B9%88%E6%98%AFgetopts&spm=1018.2226.3001.4187)
- optstring 参数
  - **指定支持的选项参数列表**，每个字符对应一个选项
  - 如果字符后面跟着冒号 :，那么在输入该选项时后面必须跟着一个参数，选项和参数之间用空格隔开.不能使用冒号 : 和问号 ? 来作为选项
  - 例如，一个有效的 optstring 参数值是 "hi:"。那么 -h 就是一个选项；-i 也是一个选项。由于在 i 后面跟着冒号 :，那么输入 -i 选项时还要提供一个参数
  - optstring 参数的选项列表不包含 - 字符，但是在实际输入选项参数时，getopts 命令要求选项参数以 - 开头，否则会报错。以上面例子来说，-h 是一个选项，但是 h 并不是一个有效的选项
  - 如果 getopts 期待一个参数，但无法解析一个，它会打印一个错误。**如果它不期望一个，*OPTARG*将被初始化为“”（一个空字符串）**
- name
  - name 参数用于保存解析后的选项名
  - 每调用一次 getopts 命令，它只解析一个选项，并把解析的选项名称存入 name 变量中
  - 解析后的值不包含 - 字符,例如解析 -h 选项后，name 变量的值是字符 h.该变量的名称不要求只能是 name 字符串，也可以是其他合法的变量名，例如 opt、arg 等等。
  - 如果要解析多个选项时，需要在 while 或者 for 循环中多次执行 getopts 命令，来逐个解析参数选项，直到解析完毕为止.解析完毕，getopts 命令会返回 false，从而退出循环
  - 如果提供的选项不在 optstring 指定的列表里面，name 的值会被设成问号 ?.但是 getopts 命令还是返回true，不会报错
- [args] 是一个可选参数，用于指定选项参数的来源
  - getopts 命令默认解析位置参数提供的参数，例如 $1、$2、...、等等
  - 如果提供了 args 参数，那么从 args 中解析选项参数，不再从位置参数中解析.也就是说，在 shell 脚本里面直接执行 getopts 命令，它默认解析的选项参数是执行脚本时提供的参数
  - 例如有一个 testgetopts.sh 脚本，那么执行 ./testgetopts.sh -a -b 命令，getopts 会解析 -a、-b 选项.如果是在函数内执行 getopts 命令，它解析的选项参数是调用函数时提供的参数
  - 例如有一个 test_getopts 函数，该函数内调用 getopts 命令，那么执行 test_getopts -a -b 语句，getopts 命令会解析 -a、-b 选项
  - 如果提供了 args 参数，getopts 命令改成解析 args 参数包含的选项.例如执行 args="-a -b"; getopts "ab" opt $args 语句，getopts 命令解析的是 args 变量指定的 "-a -b" 字符串
- 系统变量
  - Name 是存储当解析的那个字符
  - OPTARG 系统变量存储的是当前解析的值
  - **OPTIND** 存储的是当前的索引
- [shell中source、sh、bash、./的区别](https://blog.csdn.net/weixin_43726471/article/details/120800757?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522169485512416800185884324%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=169485512416800185884324&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-2-120800757-null-null.142^v94^chatsearchT3_1&utm_term=shell%E4%B8%ADsource%E3%80%81sh%E3%80%81bash%E3%80%81.%2F%E6%89%A7%E8%A1%8C%E8%84%9A%E6%9C%AC%E7%9A%84%E5%8C%BA%E5%88%AB&spm=1018.2226.3001.4187)
  - `source` 和上面的sh bash ./不同的是，它是在当前的shell程序下去执行，而上面的三种是在当前的shell下重新开一个子shell进行执行
- [exec](https://www.jianshu.com/p/60a3dae7694f)

  - exec执行命令时，不会启用新的shell进程。source和 .也不会启用新的shell进程，在当前shell中执行，设定的局部变量在执行完命令后仍然有效
  - bash或sh执行时，会另起一个子shell进程，其继承父shell进程的环境变量，其子shell进程的变量执行完后不影响父shell进程
  - exec是用被执行的命令行替换掉当前的shell进程，且exec命令后的其他命令将不再执行.例如在当前shell中执行 exec ls 表示执行ls这条命令来替换当前的shell ，即为执行完后会退出当前shell
  - 为了避免父shell被退出，一般将exec命令放到一个子shell脚本中，在父sehll中调用这个子shell脚本，调用处可以用bash xx.sh(xx.sh为存放exec命令的脚本)，这样会为xx.sh建立一个子shell去执行，当执行exec后该子shell进程就被替换成相应的exec的命令
  - 其中有一个例外：当exec命令对文件描述符操作的时候，就不会替换shell，而是操作完成后还会继续执行后面的命令！

- `su xx -c `

  - 在 `xx` 用户下执行命令， <font color=yellow> 执行此命令时并不会切换到 xx 的home目录下，而是在当前执行用户所在目录 </font>

  - <font color=yellow>注意事项</font>

    ```sh
    
    # 这种方式看不到data
    su - xiaoju -c "export data=1 && echo $data"
    
    # 换成单引号就可以了!!!!!
    su - xiaoju -c 'export data=1 && echo $data'
    
    # 这是因为shell 该表达式的含义为： 将 -c 后面的内容传递给xiaoju 用户执行 ，如果是双引号-会读取当前环境的值
    
    # 如果是单引号，则是将命令原封不动的进行传递
    ```

    