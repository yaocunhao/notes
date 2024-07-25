- [link](https://github.com/apache/brpc/blob/release-1.0/docs/cn/server.md)

- 是什么

  - `bthread` 的设计目标是提供一种轻量级的线程实现，它通过用户级的线程调度和管理，来实现高效的并发处理和低开销的上下文切换。`bthread` 的这些接口提供了丰富的功能，帮助开发者构建高性能的并发应用程序。

- 是否需要池子

  - 批量使用时，直接创建即可，不需要创建池子，bthread库内部已经管理好了

- 提供接口

  - bthread 创建和运行

    - **`bthread_start_background`** 和 **`bthread_start_urgent`**：这两个函数用于创建并启动一个新的 `bthread`。它们的主要区别在于线程的优先级和调度方式。`bthread_start_background` 用于启动常规优先级的线程，而 `bthread_start_urgent` 用于启动需要立即执行的高优先级线程。

  - bthread 同步

    - **`bthread_join`**：等待一个 `bthread` 结束并回收资源。这类似于 `pthread_join`，用于阻塞当前线程直到指定的 `bthread` 完成执行。
    - **`bthread_mutex`**：包括 `bthread_mutex_init`, `bthread_mutex_lock`, `bthread_mutex_unlock` 和 `bthread_mutex_destroy` 等函数，用于在 `bthread` 中使用的互斥锁操作。

  - 任务调度和控制

    - **`bthread_flush`**：确保之前启动的所有后台线程都已经开始执行。
    - **`bthread_about_to_quit`**：用于通知调度器当前线程即将结束，这可以帮助调度器更好地优化线程的执行和资源回收。

  - 线程局部存储（TLS）

    - **`bthread_setspecific`** 和 **`bthread_getspecific`**：用于设置和获取线程局部存储（TLS）的值。这允许线程存储和访问线程特定的数据。

  - 其它辅助功能

    - **`bthread_usleep`**：让当前线程休眠指定的微秒数。这对于实现延时或者轮询等操作很有用。

  - **传递Bthread 变量**

    - 而言之，`bthread_key_create` 在程序的初始化阶段被调用以创建一个可以跨多个 bthread 共享的键，而 `bthread_setspecific` 则用于将数据与某个 bthread 的这个键相关联。二者共同工作，为 bthread 提供了一种有效的方式来存储和访问线程局部存储（TLS）中的数据。
    - `bthread_key_create`

    ```
    用途：bthread_key_create 用于创建一个线程特定数据的键（key）。这个键可以在所有 bthread 中用来访问存储在 TLS 中的数据。每个键都关联一个可选的析构函数，当使用该键的线程结束时，析构函数会被调用，用来清理该键关联的数据。
    工作机制：当你调用 bthread_key_create 时，它会分配一个唯一的键，并将这个键与一个可选的析构函数相关联。这个键随后可以在多个 bthread 中用来存储和检索数据，而每个 bthread 都会看到其自己的数据副本。
    示例用途：初始化一个全局变量以存储对于每个 bthread 都是唯一的上下文信息。
    ```

    - bthread_setspecific

    ```
    用途：bthread_setspecific 用于将特定的数据与之前通过 bthread_key_create 创建的键相关联。这允许每个 bthread 存储其自己的、键关联的数据副本。
    工作机制：调用 bthread_setspecific 时，你需要提供一个键和一个指向数据的指针。这个函数会将数据与当前 bthread 的指定键相关联，使得这份数据只对当前 bthread 可见，从而实现了数据的线程局部存储。
    示例用途：在每个请求处理开始时存储请求上下文信息，使得这些信息在当前 bthread 的执行路径中随时可用，而不会与其他 bthread 中的请求上下文信息冲突。
    ```

  - brpc 会针对每个请求创建一个bthread吗，还是一个bthread 对应多个请求
  
    - 每个请求创建一个
    
  - bthread_key_t， 全局只需要一个就够了？
  
    - 果你的应用只需要在所有`bthread`中维护一种类型的局部数据，则确实只需要创建一个`bthread_key_t`实例。这个单一的`bthread_key_t`可以用于管理那种类型的局部数据，每个`bthread`都可以使用这个键来存储和访问自己的数据副本

