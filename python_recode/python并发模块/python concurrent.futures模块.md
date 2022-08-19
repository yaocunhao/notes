# 一、概述

[参考链接](https://blog.csdn.net/jpch89/article/details/87643972?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522165413532816782388013843%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fall.%2522%257D&request_id=165413532816782388013843&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~hot_rank-1-87643972-null-null.142^v11^pc_search_result_control_group,157^v12^control&utm_term=concurrent.futures&spm=1018.2226.3001.4187)

[python官文档](https://docs.python.org/zh-cn/3/library/concurrent.futures.html)

[python并发编程](https://python-parallel-programmning-cookbook.readthedocs.io/zh_CN/latest/index.html)

[参考文章](https://blog.csdn.net/The_Time_Runner/article/details/99652083?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522165476507316782388057594%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fall.%2522%257D&request_id=165476507316782388057594&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~rank_v31_ecpm-1-99652083-null-null.142^v11^pc_search_result_control_group,157^v13^control&utm_term=%E5%9C%A8+concurrent.py+%E4%B8%AD%E6%89%BE%E4%B8%8D%E5%88%B0%E5%BC%95%E7%94%A8+futures+&spm=1018.2226.3001.4187)

[参考文章](https://blog.csdn.net/weixin_43841688/article/details/112345541?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522165476556016781685374067%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=165476556016781685374067&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-1-112345541-null-null.142^v11^pc_search_result_control_group,157^v13^control&utm_term=python+concurrent&spm=1018.2226.3001.4187)

[futures](https://blog.csdn.net/s695811494/article/details/113062998?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522165477470816782246443073%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fall.%2522%257D&request_id=165477470816782246443073&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~rank_v31_ecpm-5-113062998-null-null.142^v11^pc_search_result_control_group,157^v13^control&utm_term=python+Future%E5%AF%B9%E8%B1%A1%E6%98%AF%E4%BB%80%E4%B9%88&spm=1018.2226.3001.4187)

[比较有意思的参考文档](https://blog.csdn.net/tianyi19/article/details/112867831?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522165484545916781685370859%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fall.%2522%257D&request_id=165484545916781685370859&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~rank_v31_ecpm-5-112867831-null-null.142^v13^pc_search_result_control_group,157^v13^control&utm_term=python+concurrent.futures+%E6%98%AF%E7%BA%BF%E7%A8%8B%E5%AE%89%E5%85%A8%E7%9A%84%E5%90%97&spm=1018.2226.3001.4187)

[参考文章](https://blog.csdn.net/freeking101/article/details/97395745?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522165485078616781483781858%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fall.%2522%257D&request_id=165485078616781483781858&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~rank_v31_ecpm-4-97395745-null-null.142^v13^pc_search_result_control_group,157^v14^control&utm_term=python+futures%E6%A8%A1%E5%9D%97%E8%87%AA%E5%B8%A6%E9%94%81%E5%90%97&spm=1018.2226.3001.4187)

## 1.1 基本概念

- concurrent.futures 是 3.2 中引入的新模块，它为异步执行可调用对象提供了高层接口。可以使用 ThreadPoolExecutor 来进行多线程编程，ProcessPoolExecutor 进行多进程编程，两者实现了同样的接口，**这些接口由抽象类 Executor 定义**
- **而多进程模块不允许子进程中再次创建子进程**
- 这个模块提供了两大类型
  - **一个是执行器类 Executor，执行器用来管理工作池**
  - **另一个是 Future 类**，执行器用来管理工作池，**通常不用直接操作 future 对象，因为有丰富的 API**

## 1.2 线程池

- concurrent.futures模块提供了高度封装的异步调用接口

  - **ThreadPoolExecutor**：线程池，提供异步调用
  - **ProcessPoolExecutor**: 进程池，提供异步调用

## 1.3 池子提供的接口

- `submit(fn, *args, **kwargs)`
  - 异步提交任务
  - submit提交后返回的结果是一个future对象，需要使用future对象.result才能获取想要的字符串等结果(线程函数的返回值)
  - `result(timeout=None)` 取得结果
- `map(func, *iterables, timeout=None, chunksize=1) `
  - 取代for循环submit的操作
- `shutdown(wait=True) `
  - 相当于进程池的pool.close()+pool.join()操作wait=True，等待池内所有任务执行完毕回收完资源后才继续wait=False，立即返回，并不会等待池内的任务执行完毕。但不管wait参数为何值，整个程序都会等到所有任务执行完毕，submit和map必须在shutdown之前
  - 也就是说主线程主阻塞在该接口的调用处，等待线程完成任务，回收资源才会继续运行
- `add_done_callback(fn)`
  - 回调函数

# 二、Executor Object 执行器对象

## 2.1 是什么

- **`concurrent.futures.Executor` 类**
  - 这个抽象类提供了一系列方法，可以用于异步执行调用
  - 它不能直接使用，只能通过子类化出来的具体类来使用

## 2.2 类中包含的方法

- **`submit(fn, \*args, \**kwargs)`**

  - 安排可调用对象 `fn(线程函数)` 以 `fn(*args, **kwargs)` 的形式执行，并返回 `Future` 对象来表示它的执行

    ```python
    import concurrent.futures
    
    # 线程函数
    def task(*args):
      sum = 0
      for i in args:
        sum += i
      return sum
        
      
    with concurrent.futures.ThreadPoolExecutor(max_workers=1) as executor: # 定义线程池
      future = executor.submit(task,1,2)  # 定义executor对象
      print(future.result()) # 获取结果
    ```

- **`map(func, \*iterables, timeout=None, chunksize=1)`**

  - 类似内置函数map(func,*iterables),但是有两点不同
    - 立即获取 `iterables` 而不会惰性获取
    - 异步执行 `func`，并支持多次并发调用
  - 它返回一个迭代器
  - 从调用 `Executor.map()` 开始的 `timeout` 秒之后，如果在迭代器上调用了 `__next__()` 并且无可用结果的话，迭代器会抛出 `concurrent.futures.TimeoutError` 异常
  - `timeout` 秒数可以是浮点数或者整数，如果设置为 `None` 或者不指定，则不限制等待时间。如果 `func` 调用抛出了异常，那么该异常会在从迭代器获取值的时候抛出
  - 当使用 ProcessPoolExecutor 的时候，这个方法会把 iterables 划分成多个块，作为独立的任务提交到进程池。这些块的近似大小可以通过给 chunksize 指定一个正整数。对于很长的 iterables，使用较大的 chunksize 而不是采用默认值 1，可以显著提高性能。对于 ThreadPoolExecutor，chunksize 不起作用
  - **注意**：不管并发任务的执行次序如何，`map` 总是基于输入顺序来返回值。`map` 返回的迭代器，在主程序迭代的时候，会等待每一项的响应

- **`shutdown(wait=True)`**

  - 告诉执行器 executor 在当前所有等待的 future 对象运行完毕后，应该释放执行器用到的所有资源。在 shutdown 之后再调用 Executor.submit() 和 Executor.map() 会报运行时错误 RuntimeError

  - 如果 wait 为 True，那么这个方法会在所有等待的 future 都执行完毕，并且属于执行器 executor 的资源都释放完之后才会返回。如果 wait 为 False，本方法会立即返回。属于执行器的资源会在所有等待的 future 执行完毕之后释放

  - 不管 wait 取值如何，整个 Python 程序在等待的 future 执行完毕之前不会退出。你可以通过 with 语句来避免显式调用本方法。with 语句会用 wait=True 的默认参数调用 Executor.shutdown() 方法

    ```python
    import shutil
    with ThreadPoolExecutor(max_workers=4) as e:
        e.submit(shutil.copy, 'src1.txt', 'dest1.txt')
        e.submit(shutil.copy, 'src2.txt', 'dest2.txt')
        e.submit(shutil.copy, 'src3.txt', 'dest3.txt')
        e.submit(shutil.copy, 'src4.txt', 'dest4.txt')
    ```
  
  - 执行器类 `Executor` 实现了上下文协议，可以用做上下文管理器。它能并发执行任务，等待它们全部完成。当上下文管理器退出时，自动调用 `shutdown()` 方法

# 三、 ThreadPoolExecutor [线程池](https://so.csdn.net/so/search?q=线程池&spm=1001.2101.3001.7020)执行器

## 3.1 是什么

- `ThreadPoolExecutor` 线程池执行器是 `Executor` 执行器的子类，通过线程池来执行异步调用
- 它管理一组工作线程，当工作线程有富余的时候，给它们传递任务
- **当属于一个 `Future` 对象的可调用对象等待另一个 `Future` 的返回时，会发生死锁 `deadlock`**

- concurrent.futures.ThreadPoolExecutor(max_workers=None, thread_name_prefix='', initializer=None, initargs=())
  这个 Executor 子类最多用 max_workers 个线程来异步执行调用





# 四、

# 总结

[参考链接](https://blog.csdn.net/haihaihai2232/article/details/91452217?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522165476374516782395373064%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fall.%2522%257D&request_id=165476374516782395373064&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~rank_v31_ecpm-1-91452217-null-null.142^v11^pc_search_result_control_group,157^v13^control&utm_term=python+confurrent%E6%A8%A1%E5%9D%97&spm=1018.2226.3001.4187)

该库统一了[线程池](https://so.csdn.net/so/search?q=线程池&spm=1001.2101.3001.7020),进程池调用,简化了编程.
是Python简单的思想哲学的体现.

唯一的缺点:无法设置线程名称. 但是这都不值一提.