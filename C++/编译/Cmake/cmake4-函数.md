- [gd](https://cmake.org/cmake/help/latest/command/function.html#command:function)

# 一、函数定义

## 1.1 基本定义

```cmake
function(<name> [<arg1> ...])
  <commands>
endfunction()
```

# 二、函数调用

- 假设有如下代码

  ```cmake
  function(foo)
    <commands>
  endfunction()
  ```

- 调用方式有如下所示，**推荐使用全部小写的形式**。在3.18 版本之中可以采用`cmake_language(CALL ...)`方式进行调用

  ```cmake
  foo()
  Foo()
  FOO()
  cmake_language(CALL foo)
  ```

  