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

  