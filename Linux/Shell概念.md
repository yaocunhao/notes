# 登录与非登录

- **登录 Shell**
  - 定义：用户通过登录（如终端登录、SSH 登录）启动的 Shell。
  - 特点：读取全局配置文件（如 `/etc/profile`）和用户的配置文件（如 `~/.bash_profile`、`~/.bash_login`、`~/.profile`）。
  - 示例启动方式：`ssh user@hostname`、`su - username`
- **非登录 Shell**：
  - 定义：在已经登录的系统中启动的新的 Shell 实例。
  - 特点：通常只读取用户的配置文件（如 `~/.bashrc`）。
  - 示例启动方式：`bash`、在终端中启动新 Shell。 只能通过exit退出不能通过logout 退出

#  交互式 Shell 和非交互式 Shell

- **交互式 Shell**：
  - **通俗点登录shell后执行命令**
  - 定义：需要用户交互的 Shell，例如用户可以输入命令并得到输出的 Shell。
  - 特点：读取配置文件如 `~/.bashrc`（如果是登录 Shell，则也读取登录 Shell 的配置文件）。
  - 示例启动方式：在终端中输入 `bash`、在登录系统时启动的 Shell。
- **非交互式 Shell**：
  - **通俗点运行shell脚本**
  - 定义：用于执行脚本或命令而不需要用户交互的 Shell。
  - 特点：不读取 `~/.bashrc`，除非在脚本中显式地 `source ~/.bashrc`。
  - 示例启动方式：执行脚本 `bash script.sh`、通过 `cron` 任务运行的脚本。



# sudo 看到变量不一致

- sudo env 和  su root 再env 看到的环境变量不一致(PATH 和 LD_LIBRARY_PATH 不一致)

- 主要分为两种

  - path 变量的不一致： 可在/etc/sudoers 之中找到 secure_path。 因此一般在安装程序时将路径link 到 /bin/ 就可以就可以
  - export 变量不一致：

  

  

  

  - [处理方式](https://unix.stackexchange.com/questions/83191/how-to-make-sudo-preserve-path)

  