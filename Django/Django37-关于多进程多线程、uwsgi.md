# 一、Django 本身是单线程的

- [link](https://blog.csdn.net/Coxhuang/article/details/89953619)

  - Django 原生为单线程序，当第一个请求没有完成时，第二个请求辉阻塞，直到第一个请求完成，第二个请求才会执行
  - <font color=yellow>Django就没有用异步，通过线程来实现并发，这也是WSGI普遍的做法，跟tornado不是一个概念</font>
  - 官方文档解释django自带的server默认是多线程
  - 测试方法：一个接口延时20S， 另外一个无延时，不会发生阻塞

- uwsgi 测试

  ```uwsgi
  # 套接字方式的 IP地址:端口号
  # socket=127.0.0.1:8000
  # Http通信方式的 IP地址:端口号
  http=0.0.0.0:8001
  #上述两种方式选择其一，在使用Nginx需要使用socket
  
  # 项目当前工作目录,这里需要换为项目文件夹的绝对路径
  chdir=/home/didi/personal-private
  
  # 项目中wsgi.py文件的目录，相对于当前工作目录
  wsgi-file=/home/didi/personal-private/Demo/trail/wsgi.py
  #是否启动主进程来管理其他进程
  master=true
  
  # 进程个数，根据电脑配置设置
  processes=1
  
  # 每个进程的线程个数
  threads=2
  
  # 服务的pid记录文件
  pidfile=/home/didi/personal-private/Demo/uwsgi.pid
  
  # 服务的日志文件位置
  daemonize=/home/didi/personal-private/Demo/uwsgi.log
  
  enable-threads=true
  skip-atexit-teardown=true
  lazy-apps=true
  vacuum=true
  die-on-term=true
  ```

  - <font color=yellow>当uwsig 单进程单线程时，会被阻塞</font>。多进程时不会被阻塞

# 二、uwsgi 启动是否会拷贝资源

- 测试方法

  - 设置一个全局的线程池

  - 更改uwsgi 进程数量来进行对比，看线程的总量数

    ```python
    1、监控线程总数量
    while :; do ps xH | wc -l ; sleep 1; done
    
    2、代码中启动一个线程池
    # 池化测试
    # pool = concurrent.futures.ThreadPoolExecutor(140)
    
    # 必须运行，加载线程池
    # def func(i):
    #   time.sleep(0.5)
    #   print(i)
      
    # for i in range(150):
    #   pool.submit(i)
    
    3、修改uwsgi配置中的进程和线程数量，然后观察总的线程数
    ```

- 结论

  - uwsgi 多进程会拷贝多份资源，多线程则是共享，并发

- 应用 

  - 开多少个进程和线程？

  - [link](https://blog.csdn.net/dqchouyang/article/details/106569657?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522169350296216800184144596%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fall.%2522%257D&request_id=169350296216800184144596&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~rank_v31_ecpm-1-106569657-null-null.142^v93^chatgptT3_1&utm_term=uwsgi%20%E8%AE%BE%E7%BD%AE%E5%A4%9A%E5%B0%91%E4%B8%AA%E8%BF%9B%E7%A8%8B%E5%92%8C%E7%BA%BF%E7%A8%8B%EF%BC%9F&spm=1018.2226.3001.4187)

    ```
    1、计算总共的核数： 物理*虚拟
    2、计算总的内存
    3、计算分配给每个cpu的内存
    4、每个cpu系统占2GB，剩下的就是用户空间
    5、默认线程堆栈1MB
    ```

    