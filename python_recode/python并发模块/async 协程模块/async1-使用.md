# 一 、基本介绍

- [gd](https://docs.python.org/3.6/library/asyncio-task.html#coroutines)

- 携程的使用方式有`装饰器`和`async def` 两种

- 装饰器是老版本，如果不需要兼容，建议使用全新关键字的方式

- 被`@asyncio.coroutine`修饰， 或者`async def` 定义的函数被称之为携程函数。通过携程函数获得的对象称之为携程对象

- 携程可以做的事情

  - 挂起协程，直到future完成，然后返回future的结果，或者引发一个异常，该异常将被传播

    ```python
    result = await future` or `result = yield from future
    ```

  - 等待另外一个携程完成结果， 协程表达式必须是对另一个协程的调用

    ```
    result = await coroutine` or `result = yield from coroutine
    ```

  - 生成一个结果，返回给正在等待的携程

    ```python
    return expression
    ```

  - 返回一个异常，给正在等待的携程

    ```python
    raise exception
    ```

- 携程的启动

  - 携程只会在事件循环时运行

  -  调用协程并不会启动它的代码运行——调用返回的协程对象在调度它的执行之前不会做任何事情

  - 启动携程的两种基本方法

    - 从另外一个携程中启动(另外一个携程已经启动了)

      ```python
      await coroutine or yield from coroutine
      ```

    - 直接启动

      ```python
      ensure_future() 、 AbstractEventLoop.create_task() 
      ```

- 为什么要有future 对象

  - 在Python协程中，Future对象是一种用于表示未来可能完成的结果或事件的占位符。它可以被用来进行异步编程，以便在协程中处理并发任务。Future对象允许我们在协程中等待一个结果或事件的完成，而不需要阻塞主线程或占用过多的资源
  - 在asyncio模块中**，Task对象是Future的子类**，并且实现了除了set_result()和set_exception()之外的所有方法。Task对象是用于包装协程的特殊Future对象，它可以被调度器调度和取消。通过使用asyncio.create_task()或asyncio.ensure_future()函数，我们可以将协程包装为Task对象。
  - 使用Future和Task对象的主要目的是实现并发和异步操作。在协程中，我们可以创建多个Future或Task对象来同时执行多个任务，并使用await关键字等待它们完成。这种方式可以提高程序的效率，允许我们在等待某个任务完成的同时，执行其他的任务。
  - 总之，Future和Task对象在Python协程中的作用是用于处理并发任务并在协程中等待结果的完成。它们是实现异步编程的重要工具

- run_until_complete 和 create_task 的区别 

  - <font color=yellow>run_until_complete 是运行事件循环，执行所有等待的任务</font>
  - run_until_complete是一个用于驱动事件循环的函数，它接受一个Future或Task对象作为参数，将这个对象添加到事件循环中等待执行，然后一直运行事件循环，直到这个对象完成（即任务执行完毕或被取消）。它返回一个表示任务结果的对象。可以将它看作是一个阻塞函数，因为在调用run_until_complete时，程序会一直阻塞在这里，直到任务完成
  - create_task是一个用于创建Task对象的函数，它接受一个可等待对象（如协程）作为参数，并返回一个Task对象。Task对象是Future的子类，它包装了可等待对象，并提供了额外的功能，如取消任务、获取任务状态等。在创建Task对象时，它会被添加到事件循环中等待执行，但不会阻塞程序的执行。可以将它看作是一个非阻塞的函数，因为它只是创建了一个任务，并将其添加到事件循环中，然后立即返回。
  - 所以，run_until_complete是一个可以阻塞程序执行的函数，用于驱动事件循环并等待某个任务完成；而create_task是一个非阻塞的函数，用于创建任务并将其添加到事件循环中，不会等待任务完成

- asyncio.run 和 loop.run_until_complete 是两种不同的方式来运行异步代码

  - syncio.run()是Python 3.7引入的新函数，它是一个高级函数，可以方便地运行异步代码。它会自动创建一个事件循环，并在代码执行完成后关闭事件循环。使用asyncio.run()时，需要将整个异步任务作为参数传递给它，它会负责运行这个任务并返回结果。
  - 而loop.run_until_complete()是在低版本的asyncio中使用的方法。它需要手动创建和关闭事件循环，并将一个协程或者一个Future对象传递给它作为参数。loop.run_until_complete()会一直运行，直到传递的协程或者Future对象完成，然后关闭事件循环并返回结果。
  - 所以，简而言之，asyncio.run()是一个更方便、更高级的方式来运行异步代码，它自动处理事件循环的创建和关闭。而loop.run_until_complete()则是在低版本的asyncio中使用的方式，需要手动处理事件循环的创建和关闭


#  二、示例

- [link_1](https://blog.csdn.net/huiqiu8041/article/details/127315487?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522168774959016800185832242%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=168774959016800185832242&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-1-127315487-null-null.142^v88^control_2,239^v2^insert_chatgpt&utm_term=%20asyncio.gather&spm=1018.2226.3001.4187)
  
  - asyncio.gather() 可一次性输入多个coroutine对象并转换为task对象加入事件循环中并发执行，返回一个包含各coroutine返回值的列表，列表元素顺序与输入coroutine对象的顺序一致。asyncio.gather() 函数虽然支持输入多个coroutine，但是不能直接用一个包含多个coroutine的列表传入，因为asyncio.gather() 要求传入参数为awaitable(可等待对象)，如果要使用列表形式传入可等待对象，可以采用列表推导的方式进行
- [Task 对象](https://blog.csdn.net/myli_binbin/article/details/123380985?ops_request_misc=&request_id=&biz_id=102&utm_term=asyncio.Task&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-2-123380985.142^v88^control_2,239^v2^insert_chatgpt&spm=1018.2226.3001.4187)
  
  - create_task只有在python3.7及以后的版本中才可以使用，就像[asyncio](https://so.csdn.net/so/search?q=asyncio&spm=1001.2101.3001.7020).run()一样，
  
- 代码

  ```python
  import asyncio
  import time
  
  # 携程的启动
  # 1、async def 就是定义一个携程对象， 直接调用携程对象并不会启动它
  # 2、调用方式有三种：
  # a:在其它已经运行的携程任务中(async def修饰的函数) 运行await 或者 yield from coroutine
  # b:future 之中的 ensure_future() 安排运行
  # c: AbstractEventLoop.create_task() 安排运行
  # async def hello():
  #   print('ello12312')
  
  
  def test1():
  
    async def hello_world():
      print("Hello World!")
      return 123
  
    # # 1、创建时间循环池
    loop = asyncio.get_event_loop()
    # 2、阻塞直到future 完成(hello_world() 就是future)。 返回值为future的返回值
    ret = loop.run_until_complete(hello_world())
    print(ret)
    loop.close()
  
  
  def test2():
    """使用ensure_future 对任务进行启动。"""
    import asyncio
  
    async def slow_operation(future):
      # 这里传入future的目的是填充返回值
      print('enter slow operation function.')
      await asyncio.sleep(1)
      # 设置返回值(必须的，否则会阻塞这里)
      future.set_result('Future is done!')
  
    loop = asyncio.get_event_loop()
    # 创建一个携程future
    future = asyncio.Future()
    # 启动任务
    asyncio.ensure_future(slow_operation(future))
    print("run")
    # 阻塞， 直接coroutine 完成
    loop.run_until_complete(future)
    print(future.result())
    loop.close()
  
  
  def test3():
    """_summary_
      验证 future 回调函数
    """
    import asyncio
  
    async def slow_operation(future):
      await asyncio.sleep(1)
      future.set_result('Future is done!')
  
    def got_result(future):
      print(future.result())
      loop.stop()
  
    loop = asyncio.get_event_loop()
    future = asyncio.Future()
    # 启动coroutine
    asyncio.ensure_future(slow_operation(future))
    # 添加回调函数
    future.add_done_callback(got_result)
    try:
      # 循环执行 event loop
      loop.run_forever()
    finally:
      loop.close()
  
  
  def test4():
    """
    所谓的任务就是一个运行的'协程函数'， 不要直接创建任务而是通过ensure_future 和 create_task() 来创建任务
    """
    import asyncio
  
    async def factorial(name, number):
      f = 1
      for i in range(2, number + 1):
        print("Task %s: Compute factorial(%s)..." % (name, i))
        await asyncio.sleep(1)
        f *= i
      print("Task %s: factorial(%s) = %s" % (name, number, f))
  
    loop = asyncio.get_event_loop()
    loop.run_until_complete(
      asyncio.gather(*[factorial("A", 2),
                       factorial("B", 3),
                       factorial("C", 4)]))
    loop.close()
  
  
  def test5():
    """批量给gather 传入任务"""
    import asyncio
  
    async def func(i):
      await asyncio.sleep(1)
      print(i)
  
    loop = asyncio.get_event_loop()
    gt = []
    for i in range(10):
      gt.append(func(i))
    loop.run_until_complete(asyncio.gather(*gt))
    loop.close()
  
  
  def test6():
    """批量给gather 传入任务"""
    import asyncio
  
    async def factorial(name, number):
      f = 1
      for i in range(2, number + 1):
        print(f"Task {name}: Compute factorial({i})...")
        await asyncio.sleep(1)
        f *= i
      print(f"Task {name}: factorial({number}) = {f}")
  
    async def main():
      # Schedule three calls *concurrently*:
      letters = ["A", "B", "C"]
  
      # 列表推到方式将“可等待对象输入”送入到asyncio.gather()函数中
      await asyncio.gather(
        *[factorial(letter, num + 2) for num, letter in enumerate(letters)])
  
    loop = asyncio.get_event_loop()
    ret = loop.run_until_complete(main())
    print(ret)
    loop.close()
  
  
  def test7():
    """批量给gather 传入任务的返回值"""
    import asyncio
  
    async def factorial(name, number):
      return name + str(number)
  
    async def main():
      # Schedule three calls *concurrently*:
      letters = ["A", "B", "C"]
  
      # 列表推到方式将“可等待对象输入”送入到asyncio.gather()函数中
      ret = await asyncio.gather(
        *[factorial(letter, num + 2) for num, letter in enumerate(letters)])
      return ret
  
    loop = asyncio.get_event_loop()
    ret = loop.run_until_complete(main())
    print(ret)
    loop.close()
  
  
  def test8():
    """
    await 是否会阻塞？
    会阻塞
    """
  
    async def func1():
      time.sleep(2)
      print('func1')
  
    async def func2():
      await func1()
      print('func2')
  
    loop = asyncio.get_event_loop()
    loop.run_until_complete(func2())
    loop.close()
  
  
  def test9():
    """
    不同的对象，聚合到一起
    可以进行聚合
    """
  
    async def func1():
      time.sleep(2)
      print('func1')
      return 1
  
    async def func2():
      print('func2')
      await func1()
      return [31212, 213, 12, 32131]
  
    f_list = [func1(), func2()]
    loop = asyncio.get_event_loop()
    ret = loop.run_until_complete(asyncio.gather(*f_list))
    print(ret)
    loop.close()
  
  
  def test10():
    """在python3.7 之中协程的不同启动方法"""
    async def func():
      print(1)
      await asyncio.sleep(2)
      print(2)
      return "test"
   
    async def main():
        print("main start")
              
        # python 3.7及以上版本的写法
        task1 = asyncio.create_task(func())
        task2 = asyncio.create_task(func())
        # python3.7以前的写法
        # task1 = asyncio.ensure_future(func())
        # task2 = asyncio.ensure_future(func())
        
        print("main end")
    
        ret1 = await task1
        ret2 = await task2
    
        print(ret1, ret2)
    
    # python3.7以后的写法
    asyncio.run(main()) 
    print("end2------")
    
    # python3.7以前的写法
    # loop = asyncio.get_event_loop()
    # loop.run_until_complete(main())
    # loop.close()
  
  
  def test11():
    """
    3.7 协程批量传入运行任务
    a、main、main2 是并行的，两者效率一致(创建好任务，然后丢入循环池运行)
    b、方式3是等待一个个的任务，是串行的
    
    """
    
    async def func(i):
      print(i)
      await asyncio.sleep(2)
      return i
    
    async def main():
      task = [func(1), func(2), func(3)]
      # gather 创建一批任务， await 进行等待完成， run 运行任务
      ret = await asyncio.gather(*task)
      for r in ret:
        print(r)
    
    async def main2():
      # 创建任务
      task_1 = asyncio.create_task(func(1))
      task_2 = asyncio.create_task(func(2))
      task_3 = asyncio.create_task(func(3))
      # 运行任务
      await task_1
      await task_2
      await task_3
    
    async def main3():
      await func(1)
      await func(2)
      await func(3)
    
    asyncio.run(main3())
    
  t1 = time.time()
  test11()
  t2 = time.time()
  print(t2 -t1)
  
  
  ```
  
  

# 三、event loop

- [asynio_event_loop1](https://blog.csdn.net/qq_37674086/article/details/113879117?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522168776227516800188574799%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fblog.%2522%257D&request_id=168776227516800188574799&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~blog~first_rank_ecpm_v1~rank_v31_ecpm-4-113879117-null-null.268^v1^koosearch&utm_term=python%E5%8D%8F%E7%A8%8B&spm=1018.2226.3001.4450)

  - loop=asyncio.get_running_loop() 返回（获取）在当前线程中正在运行的事件循环，如果没有正在运行的事件循环，则会显示错误；它是python3.7中新添加的

    loop=asyncio.get_event_loop() 获得一个事件循环，如果**当前线程**还没有事件循环，则创建一个新的事件循环loop(主线程)；

    loop=asyncio.set_event_loop(loop) 设置一个事件循环为**当前线程**的事件循环；

    loop=asyncio.new_event_loop() 创建一个新的事件循环

- [asynio event_loop2](https://blog.csdn.net/qq_37674086/article/details/113884099?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522168776040816800226515353%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=168776040816800226515353&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~baidu_landing_v2~default-1-113884099-null-null.142^v88^control_2,239^v2^insert_chatgpt&utm_term=set_event_loop&spm=1018.2226.3001.4187)

# 四、杂记

- get_event_loop 出现错误 `RuntimeError: There is no current event loop in thread 'Thread-10'.`

  - 默认情况下，在主线程中时，若没有event loop，则 asyncio 会自动创建一个新的 event loop（参见 asyncio.get_event_loop 的文档），而在一个其他线程中，则不会自动创建。
    因此在新线程中使用 apscheduler 前，需要手动设置一个 event loop，即:


    	import asyncio
    	
    	new_loop = asyncio.new_event_loop()
    	asyncio.set_event_loop(new_loop)

- [协程是否需要加锁](https://juejin.cn/post/7076325718317072415)
  - 因为线程是系统态切换，虽然同时只能有一个线程执行，但切换过程是<font color=yellow>争抢</font>的，也就会导致写操作被原子性覆盖，而协程虽然在手动切换过程中也无法保证状态一致，但是可以保证最终一致性呢？因为协程是用户态，切换过程是<font color=yellow>协作</font>的，所以写操作不会被争抢覆盖，会被顺序执行，所以肯定可以保证最终一致性。

