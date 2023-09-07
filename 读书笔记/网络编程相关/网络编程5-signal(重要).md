- [board](https://boardmix.cn/app/editor/7dw1HCC6WxywZWHjuzJH1A)



# 常用处理的信号

- [signal(SIGCHLD, SIG_DFL)](https://blog.sina.com.cn/s/blog_73e57dce0102uy6x.html)

  - ```
    执行system函数时，SIGCHLD信号，最好被显示的：
    signal( SIGCHLD, SIG_DFL )
    一下，因为system函数中，使用到了fork(),waitpid.如果父进程忽略了SIGCHID信号，waitpid就没有不能得到子进程的SIGCHLD信号，那么，处理的返回值就会有问题。system的返回值也会有问题。通常的做法是：
    signal( SIGCHLD, SIG_DFL );
    system( command );
    signal( SIGCHLD, SIG_IGN );
    ```

    

