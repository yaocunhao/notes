- `multiprocessing.active_children()`

  -  返回当前进程的所有活**子进程的列表**

  - **处理僵尸进程的作用**：在 Unix 上，如果一个进程执行完成但是没有被 join，就会变成僵尸进程。一般来说，僵尸进程不会很多，因为每次新启动进程（或者 [`active_children()`](https://docs.python.org/zh-cn/3/library/multiprocessing.html#multiprocessing.active_children) 被调用）时，所有已执行完成且没有被 join 的进程都会自动被 join

    ```python
    import multiprocessing
    
    multiprocessing.set_start_method('fork')
    pool = multiprocessing.Pool(1)
    print(multiprocessing.active_children()) # [<ForkProcess name='ForkPoolWorker-1' pid=4634 parent=4633 started daemon>]
    
    pool.close()
    pool.join()
    ```

- `multiprocessing.``cpu_count`()
  - 返回CPU数量
- `multiprocessing.``current_process`()
  - 返回当前进程对象，和`threading.current_thread()`类似

- `multiprocessing.``parent_process`()

  - 返回当前进程的父进程，对于主进程为None

- `multiprocessing.``get_all_start_methods`()

  - 返回支持进程启动的方法列表

    ```python
    print(multiprocessing.get_all_start_methods()) # ['fork', 'spawn', 'forkserver']
    ```

- `multiprocessing.``get_context`(*method=None*)
  - methid：可选为 ['fork', 'spawn', 'forkserver']，如果为 None  则返回默认上下文
  - 返回一个上下文对象，默认和multiprocessing 模块有一样的方法
- `multiprocessing.``get_start_method`(*allow_none=False*)
  - 返回启动进程的方法
  - 设置了启动方法，参数为 Flase，返回启动方法名字
  - 没设置启动方法，参数为True则返回None