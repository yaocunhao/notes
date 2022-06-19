[参考链接](https://blog.csdn.net/wohu1104/article/details/120098436?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522165485492416780366551781%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fall.%2522%257D&request_id=165485492416780366551781&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~rank_v31_ecpm-1-120098436-null-null.142^v13^pc_search_result_control_group,157^v14^control&utm_term=python+futures%E7%9A%84%E8%BF%9B%E7%A8%8B%E6%B1%A0%E8%AE%BF%E9%97%AE%E5%85%A8%E5%B1%80%E5%8F%98%E9%87%8F%E5%87%BA%E9%94%99&spm=1018.2226.3001.4187)

# 一、进程

## 1.1 不带参进程的启动

- python 操作进程的类都定义在 `multiprocessing`模块之中，该模块提供了一个Process类来代表一个进程对象，这个对象可以理解成一个独立的进程，来执行另外的是(**父进程否则监听，然后创建子进程去执行任务**)

- 和直接在linux下面创建进程对比

  - 在创建进程的时候，传入进程函数，然后通过`进程对象.start()`进行启动即可，不需要再像`fork()`创建一样通过判断进程ID来执行不同的逻辑或者进行程序替换

  ```python
  import time
  import multiprocessing  
  
  
  def test1():
      while True:
          print("--1--")
          time.sleep(1)
  
  def test2():
      while True:
          print("--2--")
          time.sleep(1)
  
  def main():
      
      p1 = multiprocessing.Process(target=test1)
      p2 = multiprocessing.Process(target=test2)
  
      p1.start()
      p2.start()
      print("main porocess")
  
   # windows 必须有这句，因为windows下子进程是通过import将主进程的代码拿过来的
   # 如果不写在main下面会导致进程重复创建，linux系统则是通过fork直接创建
  if __name__ == '__main__':
    main()
  ```

## 1.2 带参数的进程启动

```python
from multiprocessing import Process

def func(*argc,**kwargs):
    print(argc , kwargs,  "进程运行了")

if __name__ == '__main__':
    p1 = Process(target=func, args=(1,2,3))
    p1.start()

```

## 1.3 join() 等待所有子进程执行完毕

- `join()`的作用是等待进程结束

  ```python
  import time
  from multiprocessing import Process
  
  def func(x):
      for i in range(3):
          time.sleep(0.5)
          print(x, i)
  
  if __name__ == '__main__':
      p1 = Process(target=func, args=("123", ))
      p2 = Process(target=func, args=("456", ))
      p1.start()
      # p1.join()
      p2.start()
  
  # join效果展示
  # 进程一执行完了，才去执行的进程二，这是因为在进程二启动前进行了join，因此主进程陷入阻塞状态
  123 0
  123 1
  123 2
  
  456 0
  456 1
  456 2
  
  # 没有使用join效果展示
  # 交叉运行的，没有进行等待，两个进程都启动了
  123 0
  456 0
  123 1
  456 1
  123 2
  456 2
  ```

- 等待所有进程执行完毕

  - 可以用一个列表将进程对象全部添加进去，在线程对象都创建完毕之后，再批量进行等待

    ```python
    import time
    from multiprocessing import Process
    
    def func(x):
        for i in range(2): # 每个进程进来，都打印两遍
            time.sleep(0.5)
            print(x, i)
    
    if __name__ == '__main__':
        pl = []
    
        for i in range(5):
            p = Process(target=func, args=("id:"+str(i), )) # 创建进程
            pl.append(p) #  将进程加入到列表之中
            p.start()
        for p in pl:
            p.join() # 等待所有进程
            
        print("所有进程都执行完了") # 子进程全部执行完毕，再执行主进程
        
    # 可以看到，所有子进程都执行完了，才执行主进程    
    id:0 0
    id:1 0
    id:2 0
    id:3 0
    id:4 0
    id:0 1
    id:1 1
    id:2 1
    id:3 1
    id:4 1
    所有进程都执行完了
    ```

## 1.4 面向对象启动进程的方式

- 不直接使用Process类创建对象，而是通过继承Process类来实现多进程

  - 主要是对`run()`进行重写，因为`process.start() `实际上调用的就是`run()`

  ```python
  from multiprocessing import Process
  import time
  
  
  class MyProcess(Process): # 进程继承
      def __init__(self, arg='my_process'): # 构造函数
          super().__init__() # 父进程的初始化
          self.arg = arg
  
      def run(self): # 重写进程的执行函数
          print("hello")
          print(self.arg, "执行了进程")
          time.sleep(1)
          print("world")
  
  
  if __name__ == '__main__':
      p = MyProcess()
      p.start()
      print("主")
  
  # 主
  # hello
  # my_process 执行了进程
  # world
  ```

- 进一步改造，既传入了自己添加的参数，同时也可以传入`Process`的参数

  ```python
  from multiprocessing import Process
  
  
  class MyProcess(Process):
  
      def __init__(self, name, *args, **kwargs):
          # 初始化 Process 的参数
          super().__init__(*args, **kwargs)
          self.name = name
  
      def run(self):
          print(self.name, "执行了进程")
  
  
  if __name__ == '__main__':
      p1 = MyProcess("Child")
      p1.start()
  # Child 执行了进程
  ```

# 二、守护进程

- 守护进程在主进程结束的时候才会结束
- 守护进程的作用
  - **程序的报活**
  - 假如我们监控很多台服务器的运行状态，我们是让服务器自己告诉监控服务器的状态，还是监控服务器去询问服务器的状态呢？通常我们会主动上报自己的状态，这时候就可以通过守护进程来做
    - 主进程：完成自己的业务逻辑
    - 守护进程：每隔五分钟就向一台机器汇报自己的状态