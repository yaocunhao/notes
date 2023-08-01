# 一、资料收集

- [gd](https://pybind11.readthedocs.io/en/latest/faq.html)

# 二、基本绑定方式

-  PYBIND11_MODULE()宏创建一个函数，当从Python内部发出import语句时将调用该函数

- 编译方法 `g++ -O3 -Wall -shared -std=c++11 -fPIC $(python3 -m pybind11 --includes) example.cpp -o example.so`

- C++ 代码

  ```c++
  
  #include <pybind11/pybind11.h>
  namespace py = pybind11; // 重命名 命名空间
  
  int add(int i, int j) {
      return i + j;
  }
  
  
  // example 模块名
  // 第二个参数m， 为是一个类型为py::module_的变量，它是创建绑定的主接口
  // 方法module_::def()生成绑定代码，将add()函数公开给Python。
  PYBIND11_MODULE(example, m) {
      m.doc() = "pybind11 example plugin"; // optional module docstring
  
      m.def("add", &add, "A function that adds two numbers");
  }
  
  
  // PYTHON 
  import example
  example.add(1, 2)
  
  ```

- 如上述所示代码，在Python 之中调用时，无法给定参数名称等操作，需要做如下修改

  ```c++
  int add(int i, int j)
  {
    return i + j;
  }
  PYBIND11_MODULE(example, m)
  {
    m.doc() = "pybind11 example plugin"; // optional module docstring
  	
    // 常规命名
    m.def("add", &add, "A function which adds two numbers",
          py::arg("i"), py::arg("j")); // 给定参数名
   	
    // 使用 literals 命名空间，使用更短的命名方式
  	using namespace pybind11::literals;
  	m.def("add2", &add, "i"_a, "j"_a);
    
    // 使用默认参数
    m.def("add", &add, "A function which adds two numbers",
      py::arg("i") = 1, py::arg("j") = 2);
  }
  
  // Python
  def test0():
    data = example.add(i=1,j=2)
    print(data)
  ```

  

# 三、从C++导入变量

- 要从c++中公开一个值，请使用attr函数将其注册到模块中，如下所示。内置类型和一般对象(稍后会详细介绍)在作为属性赋值时自动转换，并且可以使用py::cast函数显式转换

  ```c++
  PYBIND11_MODULE(example, m) {
      m.attr("the_answer") = 42;
      py::object world = py::cast("World");
      m.attr("what") = world;
  }
  ```