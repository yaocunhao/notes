# 一、基本介绍

- concurrent是一个包，这个包里面提供了一个futures模块。模块提供了两个类，分别为进程池和线程池
- **这两个池子类继承了Executor抽象类，因此也继承了Executor的接口**
- 也就是说，**实现继承的接口，就是实现异步调用的方法**![image-20220609184121184](../../../../Library/Application Support/typora-user-images/image-20220609184121184.png)

# 二、Executor 类

- 位置：**`concurrent.futures.Executor`**

- 该**抽象类**提供异步执行的调用方法。因此需要子类继承之后，进行重写，然后进行调用

## 2.1 submit

- `submit`(*fn*, */*, **args*, ***kwargs*)

  - 参数： 线程函数，和参数

- 作用

  - 异步提交任务

  - submit提交后返回的结果是一个future对象，需要使用`future对象.result`才能获取想要的字符串等结果(线程函数的返回值)

    ```python
    from concurrent import futures
    import threading
    import time
    
    def work(*args,**kwargs):
    	print(threading.current_thread().name) # 打印线程的名字
    	time.sleep(30)
    
    
    
    with futures.ThreadPoolExecutor(None,'test_pool') as executor:
    	for  i in range(10): # 通过for循环，来从future中取出来线程，来执行下面的工作
    		executor.submit(work) # 执行完后，主线程就会进行等待
    ```

    

## 2.2 map

- `map`(*func*, **iterables*, *timeout=None*, *chunksize=1*)

  - 取代for循环submit的操作

    ```python
    from concurrent import futures
    import threading
    import time
    
    def work(*args,**kwargs):
    	print(threading.current_thread().name) # 打印线程的名字
    	time.sleep(30)
    
    
    with futures.ThreadPoolExecutor(None,'test_pool') as executor:
    	executor.map(work,[1,2,3]) # 启用三个线程，每个线程传入一个参数
    
    ```

## 2.3 shutdown

- `shutdown`(*wait=True*,  *cancel_futures=False*)

- 相当于进程池的pool.close()+pool.join()操作

- wait=True，等待池内所有任务执行完毕回收完资源后，主线程才继续执行

- wait=False，立即返回，并不会等待池内的任务执行完毕。但不管wait参数为何值，整个程序都会等到所有任务执行完毕，submit和map必须在shutdown之前

- *在 3.9 版更改:* 增加了 *cancel_futures*

  ```python
  # shutdown的作用
  
  with futures.ThreadPoolExecutor(max_workers=3) as executor:
  	for k in range(4):
  		executor.submit(work)
  	#executor.shutdown()
  	print('主线程')
  
  
  # 有shutdown()
  # thread
  # thread
  # thread
  # thread
  # 主线程
  
  # 没有shutdown
  # thread
  # thread
  # thread
  # 主线程 # 可以看到主线程线先运行了
  # thread
  
  ```

  

  ```python
  # 总体演示代码
  
  import threading
  from concurrent import futures
  import logging
  import time
  
  # 进程函数
  def worker(n):
    print(f"begin_{n}")
    time.sleep(5)
    logging.info(f"finished_{n}")
    
  # 创建线程池, 容量为3
  if __name__=='__main__': # 多进程模块之中，不允许子进程再创建子进程
    executer = futures.ProcessPoolExecutor(max_workers=3) # 获取进程池模块
    fs = []
    for i in range(3):
      future = executer.submit(worker, i)  # 执行任务，返回的是future对象
      fs.append(future)
  
    print("主进程开始执行死循环！！！")
    while True:
      time.sleep(2)
      print(threading.enumerate())  ##输出正在运行的进程
  
  
      flag = True
      for thread in fs:
        print(f"线程是否结束：{thread.done()}") # 判断进程是否结束
        flag = flag and thread.done()
        if not flag: # 最少有一个进程没有结束
          break
      
      if flag: # 进程池内的进程全部结束了
        executer.shutdown()  # 都结束了，进行进程的清空
        break 
  
  # 主进程开始执行死循环！！！
  # begin_0
  # begin_1
  # begin_2
  # [<_MainThread(MainThread, started 4438683136)>, <Thread(Thread-1, started daemon 123145319677952)>, <Thread(QueueFeederThread, started daemon 123145324933120)>]
  # 线程是否结束：False
  # [<_MainThread(MainThread, started 4438683136)>, <Thread(Thread-1, started daemon 123145319677952)>, <Thread(QueueFeederThread, started daemon 123145324933120)>]
  # 线程是否结束：False
  # [<_MainThread(MainThread, started 4438683136)>, <Thread(Thread-1, started daemon 123145319677952)>, <Thread(QueueFeederThread, started daemon 123145324933120)>]
  # 线程是否结束：True
  # 线程是否结束：True
  # 线程是否结束：True
  ```

# 三、线程池

- *class* `concurrent.futures.ThreadPoolExecutor`(*max_workers=None*, *thread_name_prefix=''*, *initializer=None*, *initargs=()*)
  - max_workers 线程的个数
    - 不填写或者None，默认为机器核数(3.5更新)，如果IO密集型，可以填写成核心的五倍
  - thread_name_prefix 线程的前缀
  - initializer 每个线程开始调用时的一个可选调用对象，initargs传递给可调用对象的参数元组(3.7版本)

# 四、进程池

- **注意点**
  - 从可调用对象中调用 [`Executor`](https://docs.python.org/zh-cn/3/library/concurrent.futures.html#concurrent.futures.Executor) 或 [`Future`](https://docs.python.org/zh-cn/3/library/concurrent.futures.html#concurrent.futures.Future) 的方法提交给 [`ProcessPoolExecutor`](https://docs.python.org/zh-cn/3/library/concurrent.futures.html#concurrent.futures.ProcessPoolExecutor) 会导致死锁
  - **也就是说，在子进程中不能再使用这种方法**
  - 如果进程池中的某一个进程被突然终止，触发异常，执行器上的操作或它的 future 对象会被冻结或死锁

- *class* `concurrent.futures.ProcessPoolExecutor`(*max_workers=None*, *mp_context=None*, *initializer=None*, *initargs=()*)

  - *max_workers* 
    - `None` 或未给出，它将默认为机器的处理器个数

  - initializer 每个进程开始调用时的一个可选调用对象，initargs传递给可调用对象的参数元组(3.7)

# 五、Future类

## 5.1 基本概念

- 位置：**`concurrent.futures.Futur`**

- Future类，表示可能已经完成或尚未完成的延迟计算
- future封装待完成的操作，可以放入[队列](https://so.csdn.net/so/search?q=队列&spm=1001.2101.3001.7020)，完成的状态可以查询，得到结果后可以获取结果
- futures这个模块下的进程池，实现的是真正的并行计算，因为它使用 ProcessPoolExecutor 类把工作分配给**多个 Python 进程**处理。因此，如果需要做 CPU 密集型处理，使用这个模块 能绕开 GIL，利用所有可用的 CPU 核心
- future对象由 [`Executor.submit()`](https://docs.python.org/zh-cn/3/library/concurrent.futures.html#concurrent.futures.Executor.submit) 创建，也就是通过池子对象调用submit接口来进行创建

## 5.2 提供的方法

- **cancel**
  - 尝试取消调用。 如果调用正在执行或已结束运行不能被取消则该方法将返回 `False`，否则调用会被取消并且该方法将返回 `True`
- **cancelled**
  - 如果调用被成功取消返回 `True`
- **running**
  - 如果调用正在执行而且不能被取消那么返回 `True`
- **done**
  - 如果调用已被取消或正常结束那么返回 `True`

- **result(*timeout=None*)**
  - 返回调用返回的值,**也就是线程函数的返回值**
  - 如果调用还没完成那么这个方法将等待 *timeout* 秒。如果在 *timeout* 秒内没有执行完成，[`concurrent.futures.TimeoutError`](https://docs.python.org/zh-cn/3/library/concurrent.futures.html#concurrent.futures.TimeoutError) 将会被触发。*timeout* 可以是整数或浮点数
  - 如果 *timeout* 没有指定或为 `None`，那么等待时间就没有限制
  - 如果 futrue 在完成前被取消则 [`CancelledError`](https://docs.python.org/zh-cn/3/library/concurrent.futures.html#concurrent.futures.CancelledError) 将被触发
  - 如果调用引发了一个异常，这个方法也会引发同样的异常
- **exception(timeout=None)**
  - 返回由调用引发的异常。如果调用还没完成那么这个方法将等待 *timeout* 秒。如果在 *timeout* 秒内没有执行完成，[`concurrent.futures.TimeoutError`](https://docs.python.org/zh-cn/3/library/concurrent.futures.html#concurrent.futures.TimeoutError) 将会被触发。*timeout* 可以是整数或浮点数
  - 如果 *timeout* 没有指定或为 `None`，那么等待时间就没有限制
  - 如果 futrue 在完成前被取消则 [`CancelledError`](https://docs.python.org/zh-cn/3/library/concurrent.futures.html#concurrent.futures.CancelledError) 将被触发。如果调用正常完成那么返回 `None`

- **add_done_callback(fn）**
  - 附加可调用 *fn* 到 future 对象
  - 当 future 对象被取消或完成运行时，将会调用 *fn*，而这个 future 对象将作为它唯一的参数(**也是一定存在的参数**)
  - 如果 future 对象已经完成或已取消，*fn* 会被立即调用
  - fn按照被加入的顺序进行调用
  - 如果可调用对象引发一个 [`Exception`](https://docs.python.org/zh-cn/3/library/exceptions.html#Exception) 异常，它会被记录下来并被忽略掉。如果可调用对象引发一个 [`BaseException`](https://docs.python.org/zh-cn/3/library/exceptions.html#BaseException) 异常，这个行为没有定义

# 六、模块函数

## 6.1concurrent.futures.wait

- `concurrent.futures.wait`(*fs*, *timeout=None*, *return_when=ALL_COMPLETED*)

  - fs：future对象，**可以不是同一个池子里面的对象**
  - *timeout* 可以用来控制返回前最大的等待秒数。 *timeout* 可以为 int 或 float 类型。 如果 *timeout* 未指定或为 `None` ，则不限制等待时间
  - return_when
    - `FIRST_COMPLETED`：函数在任意一个 `future` **完成或者被取消时返回**
    - `FIRST_EXCEPTION`：函数在任意一个 `future` **因为异常而结束时返回**。如果没有 `future` 抛出异常，它等价于 ALL_COMPLETED
    - `ALL_COMPLETED`：当**所有** `future` 完成或者被取消时函数才会返回

- 作用

  - 等待 Future 实例完成，这些实例可能由多个不同的执行器实例创建，通过 fs 指定这些 Future 实例

  - 返回具名元组，该元组有两个元素，每个元素都是一个集合

    - 第一个元素名叫 done，该集合包括已完成的 futures

    - 第二个元素名叫 not_done，该集合包括未完成的 futures

      ```python
      ret = futures.wait(fs) # fs为list，里面是future对象
      print(ret)
      
      DoneAndNotDoneFutures(
        # 已完成的
        done={<Future at 0x7fe658337390 state=finished returned NoneType>, <Future at 0x7fe658462cc0 state=finished returned NoneType>, <Future at 0x7fe6584776d8 state=finished returned NoneType>},
        
        # 未完成的
        not_done=set())
      ```

      ```python
      ret = futures.wait(fs, return_when='FIRST_COMPLETED' # 有一个完成或取消就返回
                         
      DoneAndNotDoneFutures(
      
      done={<Future at 0x7fa4e03453c8 state=finished returned NoneType>, <Future at 0x7fa4e0485668 state=finished returned NoneType>}, 
      
      not_done={<Future at 0x7fa4e0485710 state=running>})
      ```

## 6.2 concurrent.futures.as_completed

- `concurrent.futures.as_completed`(*fs*, timeout=None)

  - fs：future对象列表，**可以来自不同的池子对象**
  - 经过 `timeout` 秒之后，对返回的迭代器调用 `__next__()` 时结果仍不可用，则会抛出 `concurrent.futures.TimeoutError` 异常。`timeout` 可以是整数或者浮点数，如果 `timeout` 没有指定或者为 `None`，则不限制等待时间

- 作用

  - 当通过 fs 指定的 Future 实例**全部执行完毕或者被取消后**，**返回这些 Future 实例组成的迭代器,也就是说遍历返回值即可获取所有已经完成的future，不用自己去判断**

  - 任何在 as_completed() 调用之前就已经完成的 Future 实例会被最先生成

  - 看源码发现，实际上这是一个用到了 yield from 的生成器函数，所以调用返回一个生成器

    ```python
    import concurrent.futures
    import urllib.request
    
    URLS = ['http://www.foxnews.com/',
            'http://www.cnn.com/',
            'http://europe.wsj.com/',
            'http://www.bbc.co.uk/',
            'http://some-made-up-domain.com/']
    
    def load_url(url, timeout):
        with urllib.request.urlopen(url, timeout=timeout) as conn:
            return conn.read()
    
    with concurrent.futures.ThreadPoolExecutor(max_workers=5) as executor: # 线程池，5个对象
        
        future_to_url = []
        for url in URLS:
          future = executor.submit(load_url,url,60)
          future_to_url[future]=url
          
        #future_to_url = {executor.submit(load_url, url, 60): url for url in URLS}
    		
         # 可以传入dict，只要future对象是作为键即可
         # 因为在传入进去之后，会用set()进行强转
        for future in concurrent.futures.as_completed(future_to_url):
            url = future_to_url[future]
            try:
                data = future.result() # 获取线程函数执行返回的结果
    
            except Exception as exc:
                print('%r generated an exception: %s' % (url, exc))
            else:
                print('%r page is %d bytes' % (url, len(data)))
    ```



# 七、异常类

- *exception* `concurrent.futures.CancelledError`[¶](https://docs.python.org/zh-cn/3/library/concurrent.futures.html#concurrent.futures.CancelledError)
  - future 对象被取消时会触发。

- *exception* `concurrent.futures.TimeoutError`
  - future 对象执行超出给定的超时数值时引发。

- *exception* `concurrent.futures.BrokenExecutor`
  - 当执行器被某些原因中断而且不能用来提交或执行新任务时就会被引发派生于 [`RuntimeError`](https://docs.python.org/zh-cn/3/library/exceptions.html#RuntimeError) 的异常类。*3.7 新版功能.*

- *exception* `concurrent.futures.InvalidStateError`
  - 当某个操作在一个当前状态所不允许的 future 上执行时将被引发。*3.8 新版功能.*

- *exception* `concurrent.futures.thread.BrokenThreadPool`
  - 当 `ThreadPoolExecutor` 中的其中一个工作者初始化失败时会引发派生于 [`BrokenExecutor`](https://docs.python.org/zh-cn/3/library/concurrent.futures.html#concurrent.futures.BrokenExecutor) 的异常类。*3.7 新版功能.*

- *exception* `concurrent.futures.process.BrokenProcessPool`
  - 当 `ThreadPoolExecutor` 中的其中一个工作者不完整终止时(比如，被外部杀死)会引发派生于 [`BrokenExecutor`](https://docs.python.org/zh-cn/3/library/concurrent.futures.html#concurrent.futures.BrokenExecutor) ( 原名 [`RuntimeError`](https://docs.python.org/zh-cn/3/library/exceptions.html#RuntimeError) ) 的异常类。*3.3 新版功能.*



# 八、线程数量的设置

- cpu密集型的是n+1个线程, 或者n*2
- IO密集型，一般n*5



# 总结

- 也就是说Exce类提供了方法，future类提供了资源的收集

- [多进程模块](https://blog.csdn.net/wohu1104/article/details/120098436?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522165485492416780366551781%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fall.%2522%257D&request_id=165485492416780366551781&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~rank_v31_ecpm-1-120098436-null-null.142^v13^pc_search_result_control_group,157^v14^control&utm_term=python+futures%E7%9A%84%E8%BF%9B%E7%A8%8B%E6%B1%A0%E8%AE%BF%E9%97%AE%E5%85%A8%E5%B1%80%E5%8F%98%E9%87%8F%E5%87%BA%E9%94%99&spm=1018.2226.3001.4187)

- [闭坑链接](https://blog.csdn.net/weixin_39869378/article/details/110784973?ops_request_misc=&request_id=&biz_id=102&utm_term=python%20futures%E7%9A%84%E8%BF%9B%E7%A8%8B%E6%B1%A0%E8%AE%BF%E9%97%AE%E5%85%A8%E5%B1%80%E5%8F%98%E9%87%8F%E5%87%BA%E9%94%99&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-1-110784973.142^v13^pc_search_result_control_group,157^v14^control&spm=1018.2226.3001.4187)
  - 进程不可直接通信，因为进程是相互独立的(设置全局变量是否可以直接通信？？？？？)