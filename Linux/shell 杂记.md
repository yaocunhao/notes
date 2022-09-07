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

  

