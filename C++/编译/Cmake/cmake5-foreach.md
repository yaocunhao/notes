- [gd](https://cmake.org/cmake/help/latest/command/foreach.html?highlight=foreach)

# 一、循环列表

- 为列表中的每个值计算一组命令

  ```cmake
  foreach(<loop_var> <items>)
    <commands>
  endforeach()
  ```

- **items** 用分号或空格分隔的项的列表

- 相当于一个for循环，每次从`items`之中取出值来赋值个给`loop_var`，然后执行中间的命令

- 同时提供了 [`break()`](https://cmake.org/cmake/help/latest/command/break.html#command:break) and [`continue()`](https://cmake.org/cmake/help/latest/command/continue.html#command:continue) 函数

# 二、循环数字

- 在这个变体中，foreach遍历数字0、1、…直到(并包括)非负整数<stop>

  ```cmake
  foreach(<loop_var> RANGE <stop>)
  ```

# 三、带步长循环数字

- 在这个变量中，foreach在<step>的步骤中迭代从<开始>到最多<停止>的数字。如果没有指定<step>，则步长为1。这三个参数<start> <stop> <step>必须都是非负整数，且<stop>不能小于<start>

  ```cmake
  foreach(<loop_var> RANGE <start> <stop> [<step>])
  ```

  

# 四、详情看官方文档

