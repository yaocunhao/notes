- [gd](https://cmake.org/cmake/help/latest/command/set.html#command:set)
- [中文文档](https://blog.csdn.net/Jay_Xio/article/details/121033423?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522166799180016782414927969%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=166799180016782414927969&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-2-121033423-null-null.142^v63^wechat,201^v3^control_1,213^v2^t3_esquery_v2&utm_term=PARENT_SCOPE&spm=1018.2226.3001.4187)
- [中文文档2](https://blog.csdn.net/sinat_31608641/article/details/123101969?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522166799271016782412565401%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=166799271016782412565401&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_click~default-1-123101969-null-null.142^v63^wechat,201^v3^control_1,213^v2^t3_esquery_v2&utm_term=cmake%20set&spm=1018.2226.3001.4187)

# 一、基本介绍

- `set()`函数可以设置正常变量，缓存变量和环境变量(不会反过来影响整个系统环境，只会影响当前的CMake进程)

# 二、设置正常变量

- 语法

  ```cmake
  set(<variable> <value>... [PARENT_SCOPE])
  ```

- `variable`：只能有一个，`value`：可以有0个，1个或多个，当**value**值为空时，方法同**unset**，用于取消设置的值。当设置多个值时，会以`;`的形式连接传递给变量

- 设置变量的作用域为当前函数或者当前目录

- 如果给出了选项`PARENT_SCOPE`，那么将会修改父目录变量的值，当前目录的变量并不会被改变

#  三、设置缓存变量

- 语法

  ```cmake
  set(<variable> <value>... CACHE <type> <docstring> [FORCE])
  ```

- type 选项

  - **BOOL**：有ON/OFF，两种取值
  - **FILEPATH**：文件的全路径
  - **PATH**：目录路径
  - **STRING**：字符串(gui界面会提供下拉选项)
  - **INTERNAL**：字符串(gui界面不会提供下拉选项，这种方式更加的暴力)

- `docstring`：描述文本(字符串)

- `[FORCE]`：变量名相同，第二次调用**set**方法时，第一次的**value**将会被覆盖

# 四、设置环境变量

- 语法

  ```cmake
  set(ENV{<variable>} [<value>])
  ```

- 该命令只影响当前CMake进程，而不影响调用CMake的进程，也不影响整个系统环境，也不影响后续构建或测试进程的环境

-  如果`ENV{<variable>}`后没有参数，或者`<value>`是一个空字符串，那么该命令将清除环境变量的任何现有值

