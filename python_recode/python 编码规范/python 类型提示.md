# 一、内置类型注解

[参考链接](https://blog.csdn.net/zy010101/article/details/124319230?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522165952284916780366543728%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fall.%2522%257D&request_id=165952284916780366543728&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~pc_rank_34-3-124319230-null-null.142^v39^pc_rank_34_2&utm_term=python%20%E4%B8%AD%E7%B1%BB%E5%9E%8B%E6%8F%90%E7%A4%BA%E7%9A%84%E7%94%A8%E6%B3%95&spm=1018.2226.3001.4187)

## 1.1 非容器类型

```python
a:int = 1
b:float = 3.1415926
c:bool = False
d:str = "qwert"
```

## 1.2 容器类型

- 指明容器本身类型

  ```python
  a:dict = {"a": 1, "q": 123, 123: 456}
  b:list = [1, 12.2, 212]
  c:tuple = ("qwe", 123)    
  d:set = set(range(10))
  ```

- 指明容器内数据的类型，在python3.9之前的版本，需要从typing模块导入相应的注解函数(typing 是在 python 3.5 才有的模块)，然后进行注解

  ```python
  from typing import Dict, List, Optional, Tuple, Set, Union # 导入对应的模块
  
  a:Dict[Union[str, int], int] = {"a": 1, "q": 123, 123: 456}     
  b:List[Union[int, float]] = [1, 12.2, 212]
  c:Tuple[str, int] = ("qwe", 123)
  c1:Tuple[Union[int ,str], ...] = (12,'11',33)
  d:Set[int] = set(range(10))
  e:Optional[str] = None
  ```

  - 下面来解释一下Dict, List, Optional, Tuple, Set, [Union](https://so.csdn.net/so/search?q=Union&spm=1001.2101.3001.7020)它们的作用。需要注意的是，这不是函数调用，这里使用[]包括起来，而非().

  - Union表示只要是[]中任意一种类型即可

    ```python
      参数必须是某种类型，且至少有一个
      
      联合类型之联合类型会被展平，例如：
      Union[Union[int, str], float] == Union[int, str, float]
      
      单参数之联合类型就是该参数自身，例如：
      Union[int] == int  # The constructor actually returns int
      
      冗余的参数会被跳过，例如：
      Union[int, str, int] == Union[int, str] == int | str
      
      比较联合类型，不涉及参数顺序，例如：
      Union[int, str] == Union[str, int]
    ```

  - Dict中的第一个值是键的类型，第二个值是值的类型

  - List只能有一个值，因此如果其中包含多种类型，需要使用Union

  - Tuple比较特殊，由于tuple是不可变类型，因此上面c:Tuple[str, int]写法实际表示tuple中只能有两个元素，且第一个元素是str类型，第二个元素是int类型。但是下一行c1:Tuple[Union[int ,str], ...]使用了省略号来表示比较大的元组

  - Optional的含义实际上和Union[X, None]是一致的,表示要么是X类型，**要么是None**

- **在python3.9+的版本上，支持内置类型直接进行注解，无需从typing模块导入，简化了注解方式，在python3.7起可以使用from \**future\** import annotations来支持内置类型直接注解**。如下所示

  ```python
  a:dict[str, int] = {"abc": 123}
  b:list[Union[int, str]] = [123,"123"]
  ```

# 二、类型别名

## 2.1 普通类型别名

- 类型别名类似于C/C++中的typedef，它的使用可以给代码带来更加明确友好的说明

  ```python
  Vector_int = List[int]      # python3.9之前支持的写法
  Vector_str = list[str]      # python3.9之后支持的写法
  
  c:Vector_int = [1,2,3,4]
  d:Vector_str = ['q', 'w', 'e']
  ```

- Vector_int就是给`List[int]`起了个别名；同理，Vector_str就是给`list[str]`起了个别名。后续就可以使用Vector_int来声明变量的类型。类型别名适合用来简化复杂的类型

  ```python
  Port = int 
  Scheme = str
  Path = str
  URL = Tuple[Scheme, Path, Port]
  
  url:URL = ("https://", "home", 443)     # 复杂类型URL
  ```

- 类型不符合时， 并不会报错，而是给出提示![image-20220803190648123](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202208031906412.png)

## 2.2 NewTyoe

- 可以自定义创一个新类型

  - 主要用于类型检查

  - NewType(name, tp) **返回一个函数，这个函数返回其原本的值**

  - **静态类型检查器会将新类型看作是原始类型的一个子类**

  - tp 就是原始类型

    ```python
    # NewType
    from typing import NewType
    
    int32 = NewType("int32", int)  # 创建了一个新的类型，编译器认为这个类型是 int的子类型,其实int32 是一个函数
    num = int32(123)
    print(type(int32))  # <class 'function'>
    print(num, type(num)) # 123 <class 'int'>
    ```

  - 警告：python3.10中NewType 现在是一个类而不是一个函数。**在调用 NewType时，会有一些额外的运行时间成本**。这个成本将在3.11.0中降低

  - 对于Python而言，一般我们是不会使用NewTpye来产生子类型，更多的可能是使用class去派生内置类型，然后进行操作

    ```python
    class INT16(int):
        "有符号16整形"
        def __init__(self, num:int) -> None:
            if -32768 <= num < 32767: # 有符号15整形的范围
                super().__init__() # 调用父类构造函数(调用int的构造函数)
            else: 
                raise Exception("Number out of range")
    
    no:INT16 = INT16(-32768)
    print(no) #  -32768
    ```

    

# 三、可调用对象的类型注解

## 3.1 Callable

- 使用typing模块提供的`Callable[[ArgType], ReturnType]`来完成

  - 一般表示这个函数的参数类型，返回值类型

    ```python
    def func(a:int, b:float) ->str :
        return str(a + b)
    
    add:Callable[[int, float], str] = func     # 
    print(add(1, 2))
    ```

  - add作为一个可调用对象，我们对其进行类型注解。上面这种场景可能不是很常见，更常见的可能是在装饰器这里使用Callable来进行注解

    ```python
    # 这里是一个装饰器函数
    # 装饰f函数，Callable 的作用是表示被修饰的函数的注解，返回值也就是一个函数
    def outer(f:Callable[[str, int, str], None]) -> Callable[[str, int, str], None]:
        def inner(a:str , b: int, c: str) -> None:
            print("decorator starting")
            f(a, b, c)
            print("decorator end")
        return inner
    
    @outer
    def fun1(name:str, age:int, sex:str) -> None:
        print("name:", name)
        print("age:", age)
        print("sex:", sex)
    
    fun1("Zhao si", 22, "男")
    ```

    

## 3.2 Any 类型

- Any类型是一种特殊的类型，静态类型检查器会将每种类型视与Any类型兼容

  ```python
  c:Any = 123
  c = "123"       # OK
  ```

- **所有没有返回类型或参数类型的函数都将隐式默认为Any注解**，当你需要混合动态和静态类型的代码时，Any是一个非常好的选择。

  另外，对于没有返回值的函数（**特指抛出异常的函数**），可以使用NoReturn进行注解

  ```python
  from typing import NoReturn
  
  def raise_exception(b:bool) -> Union[NoReturn, str]:
      if not b:
          raise Exception("ERROR")
      else:
          return "OK"
  
  res:Union[NoReturn, str] = raise_exception(False)
  ```

  

