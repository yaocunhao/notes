- set-ex

  - `set -e`的意思就是当脚本中的任何一行执行失败就退出

  - `set -x`的意思就是打印已经成功执行的脚本，每一行前面会有一个“+”号，如：

    ```powershell
    + rm -f pg_all.sql pg_all.sql.gz
    ```

  - `set +x`的意思就是关闭打印日志功能

- su 命令

  - su root 和su - root的区别
    - su 默认切到 root
    - su 与su - 的区别：
      - su 是不改变当前变量
      - **su - 是切换到用户的变量**
    - **su只能获得root的执行权限，不能获得环境变量，而su - 是切换到root并获得root的环境变量及执行权限**
  - su -c命令的含义为
    - 变更账号为USER的使用者，并执行指令（command）后再变回原来使用者

- 批量删除进程

  ```bash
   kill -9 `ps -aux | grep uwsgi | awk '{print $2}'`
  ```


- dirname
  - 去除掉最后的`/`，得到真正的目录
- local
  - 声明变量，一般在函数之中使用，表示这个作用域是处于函数之中 
- <font color=yellow>在命令行中运行</font>
  
  - 只需要写脚本的时候换行就行![image-20230901001932737](../../../Library/Application Support/typora-user-images/image-20230901001932737.png)

- sudo env 和  su root 再env 看到的环境变量不一致(PATH 和 LD_LIBRARY_PATH 不一致)
  - [处理方式](https://unix.stackexchange.com/questions/83191/how-to-make-sudo-preserve-path)
  
- dirname 和 BASE_SOURCE[0] 的区别
  
  ```shell
  #!/bin/bash
  # 获取当前脚本所在目录
  SCRIPT_DIR=$(
    cd "$(dirname "$0")"
    pwd
  )
  
  
  在 bash 中执行脚本和使用 source 命令执行脚本时，SCRIPT_DIR 变量的值可能会不同。这主要是因为这两种方式处理脚本的环境和上下文的方式不同。
  
  区别解释
  bash 执行脚本:
  当你用 bash script.sh 或 ./script.sh 运行一个脚本时，系统会启动一个新的子 shell 进程来执行该脚本。这个子 shell 进程会将脚本中的 $0 设置为脚本的文件路径。
  由于脚本在一个新的子 shell 中运行，$0 表示的是脚本的路径，所以 SCRIPT_DIR 会被设置为脚本所在的目录。
  
  source 执行脚本:
  当你用 source script.sh 或 . script.sh 执行一个脚本时，脚本内容会在当前的 shell 会话中直接运行，而不是在一个新的子 shell 中。
  在这种情况下，$0 通常表示的是当前的 shell（通常是 -bash），而不是脚本的路径。因此，当脚本尝试获取其自身路径时，dirname "$0" 将返回 . 或空字符串，因为 $0 不再代表脚本路径。
  这会导致 SCRIPT_DIR 的值不是脚本所在的目录，而是当前 shell 的工作目录。
  
  如果你需要在 source 和 bash 两种执行方式下都能正确获取脚本所在的目录，可以考虑以下方法：
  
  SCRIPT_DIR=$(
    cd "$(dirname "${BASH_SOURCE[0]}")"
    pwd
  )
  ```
  
  
