# 一、绑定自定义类型

- 绑定对象： class_为c++类或结构风格的数据结构创建绑定
- init： 表示绑定构造函数， 填充模板为构造函数的参数类型
  - 如果有多重构造函数，且都需要保留，则绑定多个init 就行

- def 绑定类中的函数
- 静态成员函数可以使用class_::def_static()以同样的方式绑定
- 参数的绑定方法和前面介绍的相同
- class_::def_readwrite() 公开私有变量
- class_::def_readonly() 公开const 变量

```c++
struct Pet
{
  Pet(const std::string &name) : name(name)
  {
    printf("Init object\n");
  }
  void setName(const std::string &name_) { name = name_; }
  const std::string &getName() const { return name; }

  std::string name;
};

// class_ 为c++类或结构风格的数据结构创建绑定。
// init 构造函数绑定， 接受构造函数形参的类型作为模板实参
PYBIND11_MODULE(example, m)
{
  py::class_<Pet>(m, "Pet")
      .def(py::init<const std::string &>())
      // 注册两个函数
      .def("setName", &Pet::setName)
      .def("getName", &Pet::getName)
      .def_readwrite("name", &Pet::name) // 将私有变量处理成公有变量(python 没有私有变量)
      // lambda 表达式
      .def("__repr__",
           [](const Pet &a)
           {
             return "<example.Pet named '" + a.name + "'>";
           });
}

// Python
p = example.Pet("Molly")
p.getName()
p.getName()
print(p) // 输出的是lambda表达式得到的内容

```

- 动态属性

  - 默认情况下，从c++导出的类不支持此功能，唯一可写的属性是使用class_::def_readwrite()或class_::def_property()显式定义的属性

    ```c++
    // 测试六： 动态属性
    class Pet
    {
    public:
      Pet(const std::string &name) : name(name) {}
      void setName(const std::string &name_) { name = name_; }
      const std::string &getName() const { return name; }
    
    private:
      std::string name;
    };
    
    PYBIND11_MODULE(example, m)
    {
      py::class_<Pet>(m, "Pet",  py::dynamic_attr())
          .def(py::init<const std::string &>())
          .def_property("name", &Pet::getName, &Pet::setName);
      // ... remainder ...
    }
    
    //Python
    def test4():
      """动态属性"""
      p = example.Pet("123")
      p.name ="444"
      print(p.name)
      p.age = 100 # 动态的添加属性
      print(p.age)
    ```

  - 继承

    ```c++
    struct Pet
    {
      Pet(const std::string &name) : name(name) {}
      std::string name;
    };
    
    struct Dog : Pet
    {
      Dog(const std::string &name) : Pet(name) {
        printf("init\n");
      }
      std::string bark() const { return "woof!"; }
    };
    
    // 方法一 第一种方式将 C++ 基类指定为 的额外模板参数
    // PYBIND11_MODULE(example, m)
    // {
    // py::class_<Pet>(m, "Pet")
    //     .def(py::init<const std::string &>())
    //     .def_readwrite("name", &Pet::name);
    
    // // Method 1: template parameter:
    // py::class_<Dog, Pet>(m, "Dog")
    //     .def(py::init<const std::string &>())
    //     .def("bark", &Dog::bark);
    // }
    
    // 方法二 
    PYBIND11_MODULE(example, m)
    {
      py::class_<Pet> pet(m, "Pet"); // 下面会用到， 因此这里需要提前定义
      pet.def(py::init<const std::string &>())
          .def_readwrite("name", &Pet::name);
    
      // Method 2: pass parent class_ object:
      py::class_<Dog>(m, "Dog", pet /* <- specify Python parent type */)
          .def(py::init<const std::string &>())
          .def("bark", &Dog::bark);
    
      // Return a base pointer to a derived instance
      m.def("pet_store", []()
            { return std::unique_ptr<Pet>(new Dog("Molly")); });
    }
    
    //Python 
    def test():
      """测试继承"""
      #dog = example.Dog('dog')
      #print(dog.bark())
      
      # 派生类切割回去给基类
      p = example.pet_store()
      print(type(p))
      print(p.name)
      #p.bark() # 派生类切割回去给基类，这里是不行的
        
     // 注意点(慎用， 不符合C++语法)：
     //  如果父类之中包含了虚函数， 切割回去的基类在python 之中能够访问子类的方法
        
    
    
    ```
  
  - 重载
  
    ```c++
    struct Pet
    {
      Pet(const std::string &name, int age) : name(name), age(age) {}
    
      void set(int age_) { age = age_; }
      void set(const std::string &name_) { name = name_; }
    
      std::string name;
      int age;
    };
    PYBIND11_MODULE(example, m)
    {
      py::class_<Pet>(m, "Pet")
          .def(py::init<const std::string &, int>())
          .def("set", static_cast<void (Pet::*)(int)>(&Pet::set), "Set the pet's age")
          .def("set", static_cast<void (Pet::*)(const std::string &)>(&Pet::set), "Set the pet's name")
          .def_readwrite("name", &Pet::name) // 将私有变量处理成公有变量(python 没有私有变量)
          .def_readwrite("age", &Pet::age); // 将私有变量处理成公有变量(python 没有私有变量)
    }
    // 方法二：
    template <typename... Args>
    using overload_cast_ = pybind11::detail::overload_cast_impl<Args...>;
    
    py::class_<Pet>(m, "Pet")
        .def("set", overload_cast_<int>()(&Pet::set), "Set the pet's age")
        .def("set", overload_cast_<const std::string &>()(&Pet::set), "Set the pet's name");
    
    
    def test2():
      """测试重载"""
      # 构造函数，不能带参数名
      p = example.Pet('123', 456)
      print(p.name, p.age)
      
      # 不可传参数名， 自动根据数据类型选择重载函数进行填充
      p.set('456')
      p.set(123)
      print(p.name, p.age)
      
    ```
  
  - 枚举和内部类型
  
    ```c++
    struct Pet
    {
      enum Kind
      {
        Dog = 0,
        Cat
      };
    
      struct Attributes
      {
        float age = 0;
      };
      Pet(const std::string &name, Kind type) : name(name), type(type) {}
    
      std::string name;
      Kind type;
      Attributes attr;
    };
    
    PYBIND11_MODULE(example, m)
    {
      // m: 属于的模块， Pet：向外提供的名称，这里表示的是向外提供的类名是Pet
      py::class_<Pet> pet(m, "Pet");
      // 绑定构造函数
      pet.def(py::init<const std::string &, Pet::Kind>())
          .def_readwrite("name", &Pet::name)
          .def_readwrite("type", &Pet::type)
          .def_readwrite("attr", &Pet::attr);
      // 绑定枚举
      py::enum_<Pet::Kind>(pet, "Kind")
          .value("Dog", Pet::Kind::Dog)
          .value("Cat", Pet::Kind::Cat)
          .export_values();
      // 绑定内部类
      py::class_<Pet::Attributes>(pet, "Attributes")
          .def(py::init<>())
          .def_readwrite("age", &Pet::Attributes::age);
    }
    
    // Python
    def test3():
      """测试枚举"""  
      p = example.Pet('luckly', example.Pet.Cat)
      print(p.type) # Kind.Cat
      print(str(p.type)) # Kind.Cat
      print(p.type.name) # Cat
    ```
  
  - 直接枚举
  
    ```c++
    // 测试七：单纯枚举类型
    enum Food
    {
      beef = 0,
      chiken,
      apple
    };
    
    PYBIND11_MODULE(example, m)
    {
      py::enum_<Food>(m, "Food")
          .value("beef", beef)
          .value("chiken", chiken)
          .value("apple", apple);
    }
    
    // Python
    def test5():
      """单纯枚举类型"""
      food = example.Food
      print(food.beef.name)
      print(food.beef.value)
    ```
  
    