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
  - 每调用一次 getopts 命令，它只解析一个选项，并把解析的值存入 name 变量中
  - 解析后的值不包含 - 字符,例如解析 -h 选项后，name 变量的值是字符 h.该变量的名称不要求只能是 name 字符串，也可以是其他合法的变量名，例如 opt、arg 等等。
  - 如果要解析多个选项时，需要在 while 或者 for 循环中多次执行 getopts 命令，来逐个解析参数选项，直到解析完毕为止.解析完毕，getopts 命令会返回 false，从而退出循环
  - 如果提供的选项不在 optstring 指定的列表里面，name 的值会被设成问号 ?.但是 getopts 命令还是返回true，不会报错
- [args] 是一个可选参数，用于指定选项参数的来源
  - getopts 命令默认解析位置参数提供的参数，例如 $1、$2、...、等等
  - 如果提供了 args 参数，那么从 args 中解析选项参数，不再从位置参数中解析.也就是说，在 shell 脚本里面直接执行 getopts 命令，它默认解析的选项参数是执行脚本时提供的参数
  - 例如有一个 testgetopts.sh 脚本，那么执行 ./testgetopts.sh -a -b 命令，getopts 会解析 -a、-b 选项.如果是在函数内执行 getopts 命令，它解析的选项参数是调用函数时提供的参数
  - 例如有一个 test_getopts 函数，该函数内调用 getopts 命令，那么执行 test_getopts -a -b 语句，getopts 命令会解析 -a、-b 选项
  - 如果提供了 args 参数，getopts 命令改成解析 args 参数包含的选项.例如执行 args="-a -b"; getopts "ab" opt $args 语句，getopts 命令解析的是 args 变量指定的 "-a -b" 字符串