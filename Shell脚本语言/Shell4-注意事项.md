# 一、环境变量导入相关

- 导入环境变量

  - sh脚本本质是启动一个shell子进程，如果直接在脚本中进行环境变量的导入，实际上是只对当前子shell进行了导入，并没有对父进程的环境变量进行设置

  ```bash
  # 不直接导入的原因是，启动脚本创建一个子进程，改变的是它自己的变量
  echo "export DATA_DIR=/home/didi/docker2/data_dir" >> ~/.bashrc
  ```


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
    
    # su -xx -c 相当于起了一个进程， 如果传递的命令如果导入了环境变量，只在此次中生效
    	t.sh: export data=1, 
    	t2.sh: echo $data
    	
    	# 使用的是source 运行t1，因此后面运行t2 是可以看到环境变量data的
    	su - xiaoju -c "set -ex && source /home/xiaoju/t.sh && bash t2.sh"
    	# 使用的是bash 运行t1又启动了一个新的进程， 因此后面运行t2 是看不到环境变量data的
    	su - xiaoju -c "set -ex && bash /home/xiaoju/t.sh && bash t2.sh"
    
    ```

    