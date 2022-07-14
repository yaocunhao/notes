- 场景

  - 即使主线程出现异常，子线程也会继续执行，直到所有的线程执行完毕，才会处理主线程的异常

    ```python
    import time
    import concurrent.futures
    
    
    def work():
      time.sleep(1)
      print('1')
      time.sleep(10)
    
    
    with concurrent.futures.ThreadPoolExecutor(max_workers=10) as executor:
      for i in range(10):
        executor.submit(work)
    
      exit(1)
      print('我是主线程')
      exit(1)
    ```

    