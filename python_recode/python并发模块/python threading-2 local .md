# 一、是什么

- 是本地线程存储变量

-  为每个线程开辟一快独立的空间出来，这样空间与空间之间的数据是隔离的

- threading.local  的作用是提供线程内的局部变量， 这种变量在线程的生命周期内起作用 ， 减少同一个线程内多个函数 或者组件之间一些 公共变量的传递的复杂度

  ```python
  import threading
  import time
  
  i = threading.local()
  def task(num):
    #global i
    i = num # 如果采用的是local，那么每个线程本地都会保存一份
    time.sleep(1)
    print(threading.current_thread().name,i)
    
  
  for i in range(3):
    thread = threading.Thread(target=task,args=(i,))
    thread.start()
  
  # 使用普通的全局变量 -> 最后输出的都是最后一个带进来的值
  # Thread-2 2
  # Thread-1 2
  # Thread-3 2
  
  # 使用线程变量 -> 即使中间sleep也没有关系，因为每个线程都在本地进行了值的保存
  # *****也就是说，每个线程本地都有一份i的值
  # Thread-1 0
  # Thread-2 1
  # Thread-3 2
  ```

  