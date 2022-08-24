# 一、Process

- 在 [`multiprocessing`](https://docs.python.org/zh-cn/3/library/multiprocessing.html#module-multiprocessing) 中，通过创建一个 [`Process`](https://docs.python.org/zh-cn/3/library/multiprocessing.html#multiprocessing.Process) 对象然后调用它的 `start()` 方法来生成进程。 [`Process`](https://docs.python.org/zh-cn/3/library/multiprocessing.html#multiprocessing.Process) 和 [`threading.Thread`](https://docs.python.org/zh-cn/3/library/threading.html#threading.Thread) API 相同。 一个简单的多进程程序示例是:

  ```python
  from multiprocessing import Process
  
  def f(name):
      print('hello', name)
  
  if __name__ == '__main__':
      p = Process(target=f, args=('bob',))
      p.start()
      p.join()
  ```

# 二、进程的启动方法

- 根据平台不同，进程的启动分为三种方法：`spawn`、`fork`、`forkserver`

- 推荐使用`fork`方法

- 选择启动方法

  - `set_start_method`

  ```python
  from multiprocessing import Process
  import multiprocessing
  
  
  def f(name):
    print('hello', name)
  
   
  multiprocessing.set_start_method('fork') #设置启动方式
  p = Process(target=f, args=('bob',))
  p.start()
  p.join()
  ```

  

# 三、API

## 3.1 构造函数基本参数

- `multiprocessing` 提供了和`threading`模块等价的API接口
- *class* `multiprocessing.``Process`(*group=None*, *target=None*, *name=None*, *args=()*, *kwargs={}*, ***, *daemon=None*)[¶](https://docs.python.org/3/library/multiprocessing.html#multiprocessing.Process)
  - group：永远为None，为了兼容`threading`
  - target：进程函数
  - name：进程名
  - args：进程参数元组
  - kwargs：进程参数键值对
  -  daemon：守护进程标识，如果是 `None` （默认值），则该标志将从创建的进程继承。

## 3.2 提供API接口

### 3.2.1 和threading同样接口

| 名称              | 作用                                                       | 参数含义                                |
| ----------------- | ---------------------------------------------------------- | --------------------------------------- |
| run()             | 子类继承重写接口                                           |                                         |
| start()           | 启动进程                                                   |                                         |
| join([*timeout*]) | 进程等待                                                   | None:阻塞等待，正数为阻塞等待的最大时长 |
| name              | 进程名称，多个进程名称可以相同，仅仅作为标识，没有特殊作用 |                                         |
| is_alive()        | 标识进程是否存活，必须在                                   |                                         |
| daemon            | bool值，必须在start之前设置，初始值继承自创建进程          |                                         |

- daemon
  - 当进程退出时，它会尝试终止其所有守护进程子进程
  - 请注意，不允许在守护进程中创建子进程。这是因为当守护进程由于父进程退出而中断时，其子进程会变成孤儿进程
  -  另外，这些 不是系统的守护进程或服务，它们是正常进程。也即是说，当程序中没有非守护进程时，它们将会被终止

### 3.2.2 新接口

- **pid**
  - 返回进程ID。在生成该进程之前，这将是 `None` 
- **exitcode**
  - 子进程的退出代码。如果该进程尚未终止则为 `None`
  - 如果子进程的 [`run()`](https://docs.python.org/zh-cn/3/library/multiprocessing.html#multiprocessing.Process.run) 方法正常返回，退出代码将是 0 。 如果它通过 [`sys.exit()`](https://docs.python.org/zh-cn/3/library/sys.html#sys.exit) 终止，并有一个整数参数 *N* ，退出代码将是 *N*
  - 如果子进程由于在 [`run()`](https://docs.python.org/zh-cn/3/library/multiprocessing.html#multiprocessing.Process.run) 内的未捕获异常而终止，退出代码将是 1 。 如果它是由信号 *N* 终止的，退出代码将是负值 *-N*
- **authkey**
  - 进程身份验证(字节字符串)
  - 当`multiprocessing`初始化时，主进程使用`os.urandom()`分配一个随机字符串
  - 进程创建时，会继承父进程的身份验证秘钥，也可以通过authkey设置一个新的字符串
- **sentinel**
  - 一个是数字句柄，当进程结束时将变为 "ready"
  - 如果要使用 [`multiprocessing.connection.wait()`](https://docs.python.org/zh-cn/3/library/multiprocessing.html#multiprocessing.connection.wait) 一次等待多个事件，可以使用此值。否则调用 [`join()`](https://docs.python.org/zh-cn/3/library/multiprocessing.html#multiprocessing.Process.join) 更简单
  - Unix上，这是一个文件描述符，可以使用来自 [`select`](https://docs.python.org/zh-cn/3/library/select.html#module-select) 模块的原语
- **terminate**
  - 终止进程。 在Unix上，这是使用 `SIGTERM` 信号完成的
  - **注意：**
    - 调用该方法，不会终止当前进程的子进程，会导致它们称为孤儿进程
    - 如果进程使用了管道、通信队列、锁、信号量，使用此方法会导致数据错误、死锁等问题
- **kill**
  - 和 terminate一样
- **close**
  - 关闭进程对象，释放所有与之相关的资源
  - 关闭线程池，使其不再接收新的任务