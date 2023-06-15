[参考链接](https://blog.csdn.net/weixin_43422005/article/details/118654836?spm=1001.2101.3001.6661.1&utm_medium=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-118654836-blog-110908454.pc_relevant_aa&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-118654836-blog-110908454.pc_relevant_aa&utm_relevant_index=1)

# 一、Lock Objects

## 1.1 基本概念

- 同步锁(互斥锁)，是python之中最低等级的可操作的原子性锁，是有`_thread`模块直接实现的
- 和C++之中一样，同一时刻只能有一个线程拥有锁，没有获取锁的线程将会被挂起，等待锁的释放
- 当有锁释放时，挂起的线程之中抢到锁的将会继续执行，**至于是那个线程会抢到锁，这是未定义的**
- 提供了两种方法
  - 加锁：acquire
  - 解锁：release

## 1.2 锁对象

- `class thread.Lock`

- Lock实际上是一个工厂函数，它返回平台支持的具体Lock类的最有效版本的实例

- `acquire`(*blocking=True*, *timeout=- 1*) ->bool

  - blocking 表示阻塞或者非阻塞
    - True 没获取到锁阻塞，返回True
    - False 没拿到锁不阻塞，立即返回False
  - timeout：最长阻塞的时间
    - 前提是 blocking为True
    - -1 表示无限制等

- `release`()

  - 释放锁，无锁线程调用会导致异常

- `locked`()

  - 如果获取了锁返回True

    ```python
    import threading
    
    l = threading.Lock()
    l.acquire() # 获取锁
    print(l.locked())
    
    l.release() # 释放锁
    print(l.locked())
    ```

# 二、RLock Objects

## 2.1 基本概念

- 递归锁可以被一个线程多次调用(互斥锁会造成死锁)
- 递归锁，除了加锁解锁的概念，还新增了锁所属线程和递归级别的概念
  - **加锁和解锁的层次必须一样，否则同样会导致死锁**





# 三、线程安全

- [Python 之中的一些方法](http://www.codebaoku.com/it-python/it-python-32526.html)