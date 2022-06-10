# 一、python 多线程模块介绍

## 1.1 基本介绍

- 多线程模块支持
  - python 提供了 thread(python3中改名为_thread，不推荐使用)，threading、queue模块来支持多线程编程
- 数据共享支持
  - 通过queue模块，用户可以创建**多个线程共享数据的队列结构**

- 线程安全支持
  - thread模块提供了基本的线程和锁支持
- 线程管理支持
  - threading 提供的是更加高级的完全的线程管理
  - `threading`模块是在低级别`_thread`模块上构建的的高级别线程接口.继承`_thread`功能,易用性更强

# 二、threading模块总体概括

## 2.1 threading 模块之中的对象

- Thread：表示一个执行线程的对象
- Lock：锁原语对象(互斥锁)
- RLock：可重入锁对象，单一线程可以获得已持有的锁(递归锁)
- Condition：条件变量对象，使得一个线程等待另一个线程满足特定的“条件”
- Event：任意数量线程等待某个事件的发生，该事件发生后所有等待该事件的线程将激活
- Semaphore：为线程间共享的优先资源提供一个“计数器”，如果没有可用资源时会被阻塞
- BoundedSemaphore：和Semaphore相似，不过不允许超过初始值
- Timer：和Thread相似，不过运行前会等待一段时间
- Barrier：创建一个障碍，必须达到指定数量线程才开始运行

## 2.2 threading 模块之中的方法

| 序号 | 方法             | 描述                                                         |
| ---- | ---------------- | ------------------------------------------------------------ |
| 1    | active_count()   | **返回Thread对象当前活动的进程数**,返回数量与方法enumerate()返回的列表长度一致 |
| 2    | current_thread() | **返回当前线程对应的Thread对象**                             |
| 3    | main_thread()    | **返回主线程对象**,一般主线程是从Python开始解释(运行)的线程  |
| 4    | TIMEOUT_MAX      | **阻塞最长等待时间**                                         |
| 5    | enumerate        | **返回当前线程对象所有活动的线程列表**,列表包括后台进程,current_thread()建立的虚拟线程对象和主线程,**不包括结束的线程未启动的线程** |

# 三、threading.Thread

- Thread 是threading模块的核心类
- 每个Thread对象代表一个线程
- 创建 Thread 对象有 两种手段
  - 直接创建 Thread ，然后传入一个线程函数
  - 编写一个自定义类继承 Thread，然后复写 run() 方法，在 run() 方法中编写任务处理代码，然后创建这个 Thread 的子类

## 3.1 Thread 属性

- **构造函数**：`Thread(group=None, target=None, name=None, args=(), kwargs={}, *, daemon=None)`

  | 序号 | 方法   | 描述                                                    |
  | ---- | ------ | ------------------------------------------------------- |
  | 1    | group  | None,未来扩充线程分组时使用                             |
  | 2    | target | **线程函数**                                            |
  | 3    | name   | **线程名称**,可以设置相同，默认为Thread-N,N为小十进制数 |
  | 4    | args   | tuple类型的参数,默认为()                                |
  | 5    | kwargs | dict类型的参数,默认为{}                                 |
  | 6    | daemon | 布尔标志，表示这个线程是否是守护线程                    |

## 3.2 Thread 方法

| 序号 | 方法                   | 描述                                    |
| ---- | ---------------------- | --------------------------------------- |
| 1    | start()                | 启动线程活动                            |
| 2    | run()                  | 表示线程活动的方法,可在子类中重写该方法 |
| 3    | join()                 | **阻塞自身所在线程**                    |
| 4    | is_alive()             | 返回布尔值，判断线程是否还在运行        |
| 5    | ident (property)       | 得到线程标识符，相当于pthread_self()    |
| 6    | daemon(property)       | 布尔值,在start()之前进行调用            |
| 7    | isDaemon(),setDaemon() | 判断和设置daemon                        |
| 8    | getName()，setName()   | 获取和设置线程名称                      |

## 3.3 Thread 生命周期

- 创建对象，表示Thread内部完成初始化

- 调用start，表示线程开始运行

- thread代码正常运行结束或者是遇到异常，线程会终止

- 可以通过`is_alive()` 查询线程是否还在运行

  ```python
  import threading
  import time
  
  # 线程函数
  def task():
    for i in range(2):
      print(threading.current_thread().name, i) # 返还线程本身，然后取其名字
      time.sleep(1)
  
  # 创建线程
  thread = threading.Thread(target=task) # 创建线程对象
  thread.start() # 线程开始运行
  
  # 主线程打印
  for i in range(3):
    print(threading.current_thread().name,i)
    time.sleep(1)
    print(thread.is_alive()) # 查询线程是否还在运行
  
  
  # Thread-1 0
  # MainThread 0
  # Thread-1 1
  # True
  # MainThread 1
  # False
  # MainThread 2
  # False
  ```

## 3.4 join() 线程阻塞

- 默认情况join会一直等待对应的线程结束，但是可以通过参数进行赋值，等待规定时间

  ```python
  # timeout 单位为秒，浮点参数
  def join(self, timeout=None):
  ```

  ```python
  import threading
  import time
  
  # 线程函数
  def task():
    for i in range(2):
      print(threading.current_thread().name, i) # 返还线程本身，然后取其名字
      time.sleep(1)
  
  thread = threading.Thread(target=task) # 创建线程对象
  thread.start() # 线程开始运行
  thread.join() # 进行线程等待
  
  # 主线程打印
  for i in range(3):
    print(threading.current_thread().name,i)
    time.sleep(1)
    print(thread.is_alive()) # 查询线程是否还在运行
  
  # 进行了线程等待，所以线执行完子线程，才执行主线程
  # Thread-1 0
  # Thread-1 1
  
  # MainThread 0
  # False
  # MainThread 1
  # False
  # MainThread 2
  # False
  ```

## 3.5 daemon 属性

- daemon默认为False，主线程默认会等待子线程运行结束

- 设置为True时，主线程结束，子线程也立即结束，主线程不会进行等待

  ```python
  # -------------------默认daemon
  import threading
  import time
  
  # 线程函数
  def task():
    for i in range(2):
      print(threading.current_thread().name, i) # 返还线程本身，然后取其名字
      time.sleep(1)
  
  thread = threading.Thread(target=task) # 创建线程对象
  thread.start() # 线程开始运行
  # 主线程立马结束，但是会等待子线程
  # Thread-1 0
  # Thread-1 1
  
  
  # -------------------daemon 设置为True
  import threading
  import time
  
  # 线程函数
  def task():
    for i in range(2):
      print(threading.current_thread().name, i) # 返还线程本身，然后取其名字
      time.sleep(1)
  
  thread = threading.Thread(target=task,daemon=True) # 创建线程对象，daemon=True
  thread.start() # 线程开始运行
  # 主线程立马结束，不会等待子线程
  # Thread-1 0
  ```

## 3.6 自定义继承Thread(复写run)

- 对run方法进行重写，**run()等价于线程函数**

```python
import threading
import time

class Threading(threading.Thread): # 继承Thread
  def __init__(self,name=None):
    threading.Thread.__init__(self,name=name)
  
  # 进行方法重写
  def run(self):
    for i in range(3):
      print(threading.current_thread().name,i)
      time.sleep(1)
  

thread = Threading(name='test_thread')
thread.start()

# test_thread 0
# test_thread 1
# test_thread 2
```

