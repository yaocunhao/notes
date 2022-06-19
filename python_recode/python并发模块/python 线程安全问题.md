# 一、全局解释器锁(GIL)

- [参考链接](https://blog.csdn.net/allway2/article/details/118055423?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522165485120416782388049397%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=165485120416782388049397&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-1-118055423-null-null.142^v13^pc_search_result_control_group,157^v14^control&utm_term=+%E5%85%A8%E5%B1%80%E8%A7%A3%E9%87%8A%E5%99%A8%E9%94%81&spm=1018.2226.3001.4187)

- Python 全局解释器锁或[GIL](https://wiki.python.org/moin/GlobalInterpreterLock)，简单来说，是一个互斥锁（或锁），它只允许一个[线程](https://realpython.com/intro-to-python-threading/)控制 Python 解释器。**这意味着在任何时间点都只能有一个线程处于执行状态**。执行单线程程序的开发人员看不到 GIL 的影响，但它可能是 CPU 密集型和多线程代码中的性能瓶颈

- 由于任意时刻只能有一个线程存在，所以即使有多个cpu内核，**也只有并发(轮询)而不是并行(每个CPU同时运行)**

  - 在多线程状态下执行CPU密集型的任务，**由于只有并发，并且多个CPU也只能存在一个线程，因此多线程还会导致速度的降低**，这种增加是锁增加的获取和释放开销的结果
  - 因为GIL的存在，线程池不仅会因为锁定而变成单线程，而且还会增加执行时间

- GIL 对 I/O 绑定多线程程序的性能没有太大影响，**因为在等待 I/O 的线程之间共享锁**，也就是说在此刻是并行的

  ```python
  # 下面代码验证了，在计算密集型时，多线程反而会降低效率
  from concurrent import futures
  import time
  
  i = 10000
  def work():
  	global i
  	i+=1
  
  t1 = time.time()
  
  # 多线程CPU计算的时间 																 0.21021294593811035
  # with futures.ThreadPoolExecutor(max_workers=10) as executor:
  # 	for k in range(10000):
  # 		executor.submit(work) # 0.21021294593811035   多线程时，CPU密集型花费的时间
  
  for k in range(10000):      # 不适用线程计算花费的时间  0.0022211074829101562 不使用多线程，反而时间有提升
  	work()
    
  t2 = time.time()
  print(t2-t1) 
  ```

  

# 二、绕开GIL

[绕开链接](https://blog.csdn.net/freeking101/article/details/97395745?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522165485078616781483781858%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fall.%2522%257D&request_id=165485078616781483781858&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~rank_v31_ecpm-4-97395745-null-null.142^v13^pc_search_result_control_group,157^v14^control&utm_term=python+futures%E6%A8%A1%E5%9D%97%E8%87%AA%E5%B8%A6%E9%94%81%E5%90%97&spm=1018.2226.3001.4187)

- `concurrent.futures`模块下的进程池可以绕开。这是因为，**进程之间是独立的，所以他们的全局解释器锁也是相互独立的**，也就是说，每个子进程都可以完整的使用一个CPU，也就是说，**在python中线程不能并行，但是进程可以，同时多线程是安全的，但是多进程不是安全的**

# 三、死锁情况的发生





# 四、线程安全的情况

- 在创建多个进程之后，再在每个进程之中创建线程，此时线程也可以并行了，因此可以会导致线程安全问题的发生？
  - 猜想：进程是独立的，一个进程创建出来的线程之间是共享的，但是和其它进程创建的线程之间却是独立的，因此也是线程安全的

# 五、待验证

- 不使用线程池而是使用普通创建的方法