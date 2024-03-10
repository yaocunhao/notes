# 一、Pybind 的类型转换

## 1.1 基本概念

- `Pybind11` 这样的工具除了需要解决跨语言函数调用之外，还需要解决的问题是<font color=yellow>在Python中访问C++的基本数据类型，反之依然</font>

- 通常有下列三种方法来帮助跨语言访问数据结构

  - 一直使用C++基本数据类型， 在需要交互的地方通过Pybind11 绑定python接口(反之依然)

    ```c++
    // 1. C++ 中的原生类型，Python 中的包装器
       // py::class_部分详细介绍了如何使用公开自定义 C++ 类型。在那里，底层数据结构始终是原始的 C++ 类，而包装器提供 Python 接口。在内部，当像这样的对象从 C++ 发送到 Python 时，pybind11 只会在本机 C++ 对象上添加外部包装层。从 Python 中获取它只需剥掉包装器即可
    
    // 2.在C++ 之中需要交互的地方使用C++ 包装器， python部分不做改变
    void print_list(py::list my_list) {
        for (auto item : my_list)
            std::cout << item << " ";
    }
    // Python
    print_list([1, 2, 3])
    1 2 3
    ```

  - 在C端使用C类型，在Python端使用Python基本数据类型，Pybind11将此称为类型转换，这种情况会发生数据拷贝。在上一点之中，C++ 暴漏了对应的封装接口(_class 或者 py::list) 因此不会进行拷贝

    ```c++
    void print_vector(const std::vector<int> &v) {
        for (auto item : v)
            std::cout << item << "\n";
    }
    
    print_vector([1, 2, 3])
    1 2 3
      
    // 自动进行list和vector的类型转换
    // 但是这里进行拷贝会产生一定的消耗
    ```

    
    

- 使用类型转换是最自然的选择，因为在不同语言之中本机类型无处不在，主要的缺点是必须在每次Python↔C转换上**生成数据副本**:这是必需的，因为相同类型的C和Python版本通常不会具有相同的内存布局。对于大型数据结构来说可能会变得相当昂贵。这可以通过使用自定义包装器覆盖自动转换来避免（即上述方法 1）。这需要一些手动操作，更多详细信息可在[“制作不透明类型”](https://pybind11.readthedocs.io/en/latest/advanced/cast/stl.html#opaque)部分中找到。

## 1.2 类型转换的拷贝情况

- 由上述可知，在类型转换的过程中，会发生数据的拷贝，从而产生额外的消耗。解决这种情况一般有下述几种办法

  - 在C++ 之中进行类封装，然后 _class 进行暴漏

  - 在C++ 之中使用`py::object` 类型

    ```c++
    #include <iostream>
    using namespace std;
    #include <pybind11/pybind11.h>
    namespace py = pybind11; // 重命名 命名空间
    #include <vector>
    #include <pybind11/stl.h>
    #include <pybind11/functional.h>
    #include <pybind11/chrono.h>
    
    // g++ -O3 -Wall -shared -std=c++11 -fPIC $(python3 -m pybind11 --includes) example.cpp -o example.so -l pthread -l proj -I ~/personal-private/C/
    
    class MyClass
    {
    public:
      MyClass(int a = 10) : _a(a)
      {
        cout << "Constructor Myclas" << endl;
      }
      MyClass(const MyClass &obj)
      {
        _a = obj._a;
        cout << "Myclass 拷贝构造" << endl;
      }
    
      ~MyClass()
      {
        cout << "Destory Myclass" << endl;
      }
    
      int _a;
    };
    
    class MyClassList
    {
    public:
      MyClassList(vector<MyClass> my_class_list)
      {
        cout << "my class list 构造" << endl;
        for (auto &my_class : my_class_list)
        {
          _my_class_list.push_back(my_class);
        }
      };
      MyClassList(const MyClassList &my_class_list)
      {
        cout << "my class list copy construct." << endl;
      }
      ~MyClassList()
      {
        cout << "my class list destory" << endl;
      }
      vector<MyClass> _my_class_list;
    };
    
    // 下方两个函数测试引用和非引用
    void MyFunction(MyClass &obj)
    {
      cout << "my function" << endl;
    }
    
    void MyFunctionNoRef(MyClass obj)
    {
      cout << "my function no ref" << endl;
    }
    
    // 下方两个函数测试引用和非引用(vector 版本)
    void MyFunctionVector(vector<MyClass> &objs)
    {
      cout << "my vector function" << endl;
    }
    
    void MyFunctionNoRefVector(vector<MyClass> objs)
    {
      cout << "my vector function no ref" << endl;
    }
    // 改造之后使得vector 引用生效
    void MyFunctionVectorPylist(py::list &objs)
    {
      cout << "my vector function py::list" << endl;
    }
    void MyFunctionVectorPylistNoRef(py::list objs)
    {
      cout << "my vector function py::list no ref" << endl;
    }
    
    // 不使用py::list进行改造，而是自己创建个类
    void MyFunctionVectorMyList(MyClassList &my_class_List)
    {
      cout << "my vector function my_class_Listr" << endl;
    }
    
    
    PYBIND11_MODULE(example, m)
    {
    
      pybind11::class_<MyClass>(m, "MyClass")
          .def(pybind11::init<int>());
      pybind11::class_<MyClassList>(m, "MyClassList")
          .def(pybind11::init<vector<MyClass>>());
      m.def("my_function", &MyFunction);
      m.def("my_function_no_ref", &MyFunctionNoRef);
      m.def("my_function_vector", &MyFunctionVector);
      m.def("my_function_no_ref_vector", &MyFunctionNoRefVector);
      m.def("my_function_vector_py_list", &MyFunctionVectorPylist);
      m.def("my_function_vector_py_list_no_ref", &MyFunctionVectorPylistNoRef);
      m.def("my_function_vector_my_list", &MyFunctionVectorMyList);
    }
    
    // python
    import example
    
    my_class_1 = example.MyClass(123)
    my_class_2 = example.MyClass(1234)
    my_class_3 = example.MyClass(1235)
    my_class_4 = example.MyClass(1)
    my_class_list = [my_class_1, my_class_2, my_class_3, my_class_4]
    
    # 1、测试引用和非引用是否生效
    example.my_function(my_class_1)
    example.my_function_no_ref(my_class_1)
    # 结论： 引用是生效的
    
    # 2、测试 list->vector 引用和非引用是否生效
    example.my_function_vector(my_class_list)
    example.my_function_no_ref_vector(my_class_list)
    # 结论： vector 之中引用是不生效的，都会发生拷贝
    
    # 3、list->vector 进行改造，使得引用生效
    # 改造方式1：在C++ 之中使用py::List 进行参数接收
    # 此时在C++之中 py:list 带不带引用都是可以的
    example.my_function_vector_py_list(my_class_list)
    example.my_function_vector_py_list_no_ref(my_class_list)
    
    # 改造方式2：没什么用，因为构造MyClassList 同样需要进行一些拷贝操作
    print("改造2")
    my_class_list = example.MyClassList(my_class_list)
    print("改造2---")
    example.my_function_vector_my_list(my_class_list)
    ```

## 1.3 不透明类型的制作

- 由于python容器->Stl 容器会发生拷贝，除了上述的解决方案，不透明类型的制作也是一个方案。
- 比如将vector<MyClass> 制作成不透明类型，然后通过`_class` 暴漏vector相关方法，然后在Python 之中进行使用即可。 不透明类型在C++ 之中不论是否引用都不会再次拷贝



## 总体相关代码

```
#include <iostream>
using namespace std;
#include <pybind11/pybind11.h>
namespace py = pybind11; // 重命名 命名空间
#include <vector>
#include <pybind11/stl.h>
#include <pybind11/functional.h>
#include <pybind11/chrono.h>
#include <pybind11/stl_bind.h>

// g++ -O3 -Wall -shared -std=c++11 -fPIC $(python3 -m pybind11 --includes) example.cpp -o example.so -l pthread -l proj -I ~/personal-private/C/
/*
{
  class Point
  {
  public:
    Point(double _lon = 0, double _lat = 0, std::string _zone = "0")
        : lon(_lon), lat(_lat), zone(_zone) {}
    double lon;
    double lat;
    std::string zone;

    std::string get_pj_init_plus_string()
    {
      std::string s =
          std::string(" +proj=utm +zone=") + zone + std::string(" +ellps=WGS84");
      return s;
    }
  };

  struct Pet
  {
    Point Print()
    {
      std::cout << "123" << std::endl;
      return Point(1.1, 1.2, "ccc");
    }
  };

  // class_ 为c++类或结构风格的数据结构创建绑定。
  // init 构造函数绑定， 接受构造函数形参的类型作为模板实参
  PYBIND11_MODULE(example, m)
  {
    pybind11::class_<Point>(m, "Point")
        .def(pybind11::init<double, double, string>())
        .def("get_pj_init_plus_string", &Point::get_pj_init_plus_string)
        .def_readwrite("lon", &Point::lon)
        .def_readwrite("lat", &Point::lat);

    py::class_<Pet>(m, "Pet")
        .def(py::init<>())
        .def("Printf", &Pet::Print);
  }
}
*/

class MyClass
{
public:
  MyClass(int a = 10) : _a(a)
  {
    cout << "Constructor Myclas" << endl;
  }
  MyClass(const MyClass &obj)
  {
    _a = obj._a;
    cout << "Myclass 拷贝构造" << endl;
  }

  ~MyClass()
  {
    cout << "Destory Myclass" << endl;
  }

  int _a;
};

class MyClassList
{
public:
  MyClassList(vector<MyClass> my_class_list)
  {
    cout << "my class list 构造" << endl;
    for (auto &my_class : my_class_list)
    {
      _my_class_list.push_back(my_class);
    }
  };
  MyClassList(const MyClassList &my_class_list)
  {
    cout << "my class list copy construct." << endl;
  }
  ~MyClassList()
  {
    cout << "my class list destory" << endl;
  }
  vector<MyClass> _my_class_list;
};

// 下方两个函数测试引用和非引用
void MyFunction(MyClass &obj)
{
  cout << "my function" << endl;
}

void MyFunctionNoRef(MyClass obj)
{
  cout << "my function no ref" << endl;
}

// 下方两个函数测试引用和非引用(vector 版本)
void MyFunctionVector(vector<MyClass> &objs)
{
  cout << "my vector function" << endl;
}

void MyFunctionNoRefVector(vector<MyClass> objs)
{
  cout << "my vector function no ref" << endl;
}
// 改造之后使得vector 引用生效
void MyFunctionVectorPylist(py::list &objs)
{
  cout << "my vector function py::list" << endl;
}
void MyFunctionVectorPylistNoRef(py::list objs)
{
  cout << "my vector function py::list no ref" << endl;
}

// 不使用py::list进行改造，而是自己创建个类
void MyFunctionVectorMyList(MyClassList &my_class_List)
{
  cout << "my vector function my_class_Listr" << endl;
}

// 使得vector<Myclass>不透明
PYBIND11_MAKE_OPAQUE(std::vector<MyClass>);

PYBIND11_MODULE(example, m)
{
  pybind11::class_<std::vector<MyClass>>(m, "MyClassVector")
      .def(pybind11::init<>())
      .def("clear", &std::vector<MyClass>::clear)
      .def("pop_back", &std::vector<MyClass>::pop_back)
      // 成员函数指针、返回值策略
      .def("push_back", (void(std::vector<MyClass>::*)(const MyClass &)) & std::vector<MyClass>::push_back, py::return_value_policy::reference_internal) // Specify the correct function signature
      .def("__len__", [](const std::vector<MyClass> &v)
           { return v.size(); });

  pybind11::class_<MyClass>(m, "MyClass")
      .def(pybind11::init<int>());

  pybind11::class_<MyClassList>(m, "MyClassList")
      .def(pybind11::init<vector<MyClass>>());
  m.def("my_function", &MyFunction);
  m.def("my_function_no_ref", &MyFunctionNoRef);
  m.def("my_function_vector", &MyFunctionVector);
  m.def("my_function_no_ref_vector", &MyFunctionNoRefVector);
  m.def("my_function_vector_py_list", &MyFunctionVectorPylist);
  m.def("my_function_vector_py_list_no_ref", &MyFunctionVectorPylistNoRef);
  m.def("my_function_vector_my_list", &MyFunctionVectorMyList);
}

// Python
import example


# 测试类型转换的拷贝情况
def test_1():
  my_class_1 = example.MyClass(123)
  my_class_2 = example.MyClass(1234)
  my_class_3 = example.MyClass(1235)
  my_class_4 = example.MyClass(1)
  my_class_list = [my_class_1, my_class_2, my_class_3, my_class_4]
  # 1、测试引用和非引用是否生效
  example.my_function(my_class_1)
  example.my_function_no_ref(my_class_1)
  # 结论： 引用是生效的

  # 2、测试 list->vector 引用和非引用是否生效
  example.my_function_vector(my_class_list)
  example.my_function_no_ref_vector(my_class_list)
  # 结论： vector 之中引用是不生效的，都会发生拷贝

  # 3、list->vector 进行改造，使得引用生效
  # 改造方式1：在C++ 之中使用py::List 进行参数接收
  # 此时在C++之中 py:list 带不带引用都是可以的
  example.my_function_vector_py_list(my_class_list)
  example.my_function_vector_py_list_no_ref(my_class_list)

  # 改造方式2：没什么用，因为构造MyClassList 同样需要进行一些拷贝操作
  print("改造2")
  my_class_list = example.MyClassList(my_class_list)
  print("改造2---")
  example.my_function_vector_my_list(my_class_list)


# test_1()


# 制作不透明类型
def test2():
  my_class_1 = example.MyClass(123)
  my_class_2 = example.MyClass(1234)
  my_class_list = example.MyClassVector()
  my_class_list.push_back(my_class_1)
  my_class_list.push_back(my_class_2)
  print("函数调用")
  # 使用不透明类型后，不论是否引用都不会发生拷贝了
  # 这里的麻烦点在与不透明STL 类型的制作
  example.my_function_vector(my_class_list)
  example.my_function_no_ref_vector(my_class_list)

test2()
```



# 二、字符串传递

## 2.1 Python 调用 C++ 接口

- C++ 可以使用`std::string` 和 `char *` 接收python传递过来的字符串。只要使用的是utf8 编码，都可以进行传递
- C++ 接收python字节对象和上述一致， 如果只允许接收字节一项，则需要使用`py::bytes`

## 2.2 C++ 返回字符串给Python

- 当C++返回值如2.1中所述，返回的将是utf8格式的字符串，如果转换失败，pybind11 抛出异常`UnicodeDecodeError`

### 2.2.1 显示转换

- 如果某些C代码构造的std::string不是UTF-8字符串，则可以执行显式转换并返回py::str对象。显式转换与隐式转换具有相同的开销

### 2.2.2 返回C++字符串而不进行转换

- string 不是表示文本，而是字节，则可以转换成bytes
- 请注意不对称:pybind11将在不编码的情况下将字节转换为std::string，但不能隐式地将std::string转换回字节。



