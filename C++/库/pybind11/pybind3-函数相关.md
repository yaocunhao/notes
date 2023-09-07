# 一、返回值策略

- [link](https://blog.csdn.net/BlowfishKing/article/details/115765518?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522168983667616782427427734%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fall.%2522%257D&request_id=168983667616782427427734&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~rank_v31_ecpm-1-115765518-null-null.142^v90^control_2,239^v2^insert_chatgpt&utm_term=pybind11%20%E8%BF%94%E5%9B%9E%E5%80%BC%E9%97%AE%E9%A2%98&spm=1018.2226.3001.4187)

- C++和python使用根本上就不一样的内存管理方法和对象生命周期管理方法。C++是对栈变量按照作用域来析构，对堆变量使用手动delete来析构。Python则是使用基于计数的垃圾回收机制。当提供C++函数接口给python时，<font color=yellow>如果返回类型不是基本类型，这种内存管理的差异会产生一些问题。</font>

- 只是看类型信息，看不出来Python是否应该接管返回值的所有权并最终回收它的资源，或是由C++端来处理。因为这个原因，pybind11提供了一些返回值策略标记传递给 module_::def()和class_::def()函数

- 默认的策略是return_value_policy::automatic（自动管理返回值)

  ```C++
  struct Person
  {
    int age;
    int gender;
  };
  Person *one_person = new Person();
  Person obj;
  Person get_one_person() { return obj; }
  
  PYBIND11_MODULE(example, m)
  {
    // C++是对栈变量按照作用域来析构，对堆变量使用手动delete来析构。Python则是使用基于计数的垃圾回收机制。
    // 当提供C++函数接口给python时，如果返回类型不是基本类型，这种内存管理的差异会产生一些问题
    // 因此当返回的不是基本类型时， 这种内存管理可能会发生一些错误.当python的垃圾回收器最终删除python包装时，pybind11也会尝试析构这个C++对象（使用delete操作符.这时整个应用程序最终会crash，尽管原因非常隐蔽，其实是静态对象内存损坏。
    m.def("get_one_person", &get_one_person); // <-- 严重危险行为，可能导致程序crashed
  }
  
  // Python
  def test7():
    get_one_person = example.get_one_person
    one_person = get_one_person() // 得到的是C++ 的基本类型
    print('end!!!!')
  ```

  

