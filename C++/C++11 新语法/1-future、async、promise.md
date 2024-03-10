# 零、参考

- [link](https://zhuanlan.zhihu.com/p/465092056)

# 一、std::future

```c++
// std::future 是 C++ 标准库中的一个类，用于表示可能在将来会返回结果的异步操作。
// 您可以使用 std::future 来异步获取一个任务的结果，或者等待一个任务的完成。
// 与 std::thread 不同，std::future 通常用于异步计算或异步 I/O 操作，而不是直接创建线程。

// 通过在一个线程中启动一个异步任务，并在另一个线程中获取其结果，我们可以充分利用多核处理器和提高程序的性能
// std::future 可以与 std::promise 和 std::packaged_task 一起使用，以实现从一个线程向另一个线程传递值的功能

#include <iostream>
#include <future>

int main()
{
  std::future<int> result = std::async([]()
                                       { return 42; });

  // 在这里进行其他操作
  int value = result.get(); // 获取异步操作的结果
  std::cout << "异步操作的结果为：" << value << std::endl;

  return 0;
}

```

# 二、std::async

```c++
// std::async 是 C++ 标准库中的一个函数模板，用于启动一个异步任务。它返回一个 std::future 对象，允许您在将来获取异步操作的结果。

// 您可以使用 std::async 来执行一个函数或函数对象，并指定其执行策略（例如，立即执行或延迟执行
// 然后在需要时获取结果。通常情况下，std::async 会自动在新线程中执行任务，但也可能在当前线程中执行，这取决于执行策略和底层实现。

#include <iostream>
#include <future>

int main()
{
  // 启动一个异步任务，并返回一个 std::future 对象
  std::future<int> result = std::async(std::launch::async, []()
                                       { return 42; });

  // 这里可以进行其他操作

  // 获取异步任务的结果
  int value = result.get();
  std::cout << "异步操作的结果为：" << value << std::endl;

  return 0;
}

```



# 三、std::promise

```c++
// std::promise 是 C++ 标准库中的一个类模板，用于在一个线程中设置值
// 并在另一个线程中获取该值。它通常与 std::future 一起使用，使得一个线程可以向另一个线程传递一个值或异常。

// 您可以使用 std::promise 来创建一个“承诺”，然后将值通过 set_value 方法设置给 std::future 对象
// 然后在其他线程中，通过 get_future 获取与 std::promise 关联的 std::future 对象，并通过它获取被设置的值

#include <iostream>
#include <future>

void setValue(std::promise<int>& prom) {
    int value = 42;
    prom.set_value(value); // 设置值给 promise
}

int main() {
    std::promise<int> prom;
    std::future<int> fut = prom.get_future(); // 获取关联的 future

    std::thread t(setValue, std::ref(prom)); // 在新线程中设置值

    // 在主线程中获取设置的值
    int value = fut.get();
    std::cout << "获取的值为：" << value << std::endl;

    t.join();

    return 0;
}

```



# 四、std::packaged_task

- packaged_task 的作用就是提供一个不同线程之间的数据同步机制，它可以存储一个函数操作，并将其返回值传递给对应的 future， 而这个 future 在另外一个线程中也可以安全地访问到这个值。

```c++
#include <iostream>
#include <cmath>
#include <thread>
#include <future>
#include <functional>
 
int f(int x, int y) { return std::pow(x,y); }
 
void task_lambda()
{
    std::packaged_task<int(int,int)> task([](int a, int b) {
        return std::pow(a, b); 
    });
    std::future<int> result = task.get_future();
 
    task(2, 9);
 
    std::cout << "task_lambda:\t" << result.get() << '\n';
}
 
void task_bind()
{
    std::packaged_task<int()> task(std::bind(f, 2, 11));
    std::future<int> result = task.get_future();
 
    task();
 
    std::cout << "task_bind:\t" << result.get() << '\n';
}
 
void task_thread()
{
   // 存储f函数
    std::packaged_task<int(int,int)> task(f);
    std::future<int> result = task.get_future();
 		// 将f函数move给线程，作为线程函数
    std::thread task_td(std::move(task), 2, 10);
    task_td.join();
 
    std::cout << "task_thread:\t" << result.get() << '\n';
}
 
int main()
{
    task_lambda();
    task_bind();
    task_thread();
}

```

# 五、小结

- promise 表示承诺对象，可以在新起的线程中设置值，然后在另外线程中获取promise的future对象，然后获取future对象的值。以此来达到不同线程数据传递的作用
- async 则是结合了thread和future的特性
-  packaged_task 可以将函数包装为异步任务，然后可以使用线程、线程池或其他异步执行方式来执行该任务

