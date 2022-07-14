# 一、脚本语言认识

- 脚本语言的第一行

  - `#!/bin/bash`
  - `#！`是一个约定标记，告诉系统这个脚本需要用什么解释器来执行，即使用哪一种shell

- 脚本语言作为可执行程序
  - 比如给`test.sh`加上`x`权限，就可以`./test`运行了

- 脚本语言作为解释器参数
  - 直接运行解释器 `/bin/sh test.sh`


# 二、Shell变量

## 2.1 变量的基本操作

- 定义变量
  - name="tom"
  - 等号左右不能有空格
- 使用变量
  - echo $name
  - echo ${name},`{}`只是为了区分变量边间
- 只读变量
  - readonly name
- 删除变量
  - unset name
  - uunset 不能删除只读变量

## 2.2 变量类型

- 局部变量
  - 局部变量在脚本或命令中定义，仅在当前shell实例中有效，其他shell启动的程序不能访问局部变量
- 环境变量
  - 所有的程序，包括shell启动的程序，都能访问环境变量，有些程序需要环境变量来保证其正常运行。必要的时候shell脚本也可以定义环境变量
- shell 变量
  - shell变量是由shell程序设置的特殊变量。shell变量中有一部分是环境变量，有一部分是局部变量，这些变量保证了shell的正常运行

## 2.3 shell 字符串

- 字符串可以用单、双、无引号

- 单引号字符串的限制

  - 单引号里面的任何字符都会原样的输出，单引号字符串中的变量是无效的
  - 单引号字串中不能出现单独一个的单引号（对单引号使用转义符后也不行），但可成对出现，作为字符串拼接使用

- 双引号

  - 双引号里可以有变量

  - 双引号里可以出现转义字符

    ```bash
    实例
    your_name="runoob"
    str="Hello, I know you are \"$your_name\"! \n"
    echo -e $str
    输出结果为：
    Hello, I know you are "runoob"! 
    
    # 如果使用单引号
    str='Hello, I know you are \"$your_name\"! \n'
    # 变量不可以被识别
    Hello, I know you are \"$your_name\"! 
    ```

- 拼接字符串
  
  ```bash
  your_name="runoob"
  
  # 使用双引号拼接
  greeting="hello, "$your_name" !"
  greeting_1="hello, ${your_name} !"
  echo $greeting  $greeting_1
  # hello, runoob ! hello, runoob !
  
  
  # 使用单引号拼接
  greeting_2='hello, '$your_name' !'
  greeting_3='hello, ${your_name} !'
  echo $greeting_2  $greeting_3
  # hello, runoob ! hello, ${your_name} !
  # 可见第一个外层使用单引号，内层也是使用了成对的单引号，因此可以可以进行拼接
  # 第二个字符串内部没有使用' 进行拼接，因此不能识别变量
  ```
  
- 获取字符串长度

  ```bash
  string="12345"
  echo ${#string} # 5
  ```

- 提取子字符串

  ```bash
  string="12345"
  echo ${string:2:2} # 34 从下标为2的字符开始，提取2个字符
  ```

- 查找子字符串

  ```bash
  string="12345cc222"
  # 在字符串string中寻找子字符串 c5 ，那个字符先出现则返回那个字符的下标
  echo `expr index "$string" c5` # 5
  ```

# 三、shell 数组

## 3.1 基本概念

- bash支持一维数组（不支持多维数组），并且没有限定数组的大小。

  类似于 C 语言，数组元素的下标由 0 开始编号。获取数组中的元素要利用下标，下标可以是整数或算术表达式，其值应大于或等于 0

## 3.2 数组的定义

- 在Shell之中，用括号来表示数组，数组中的元素用“空格”分割开，一般的数组定义形式为

  - `数组名=(值1 值2 ... 值n)`

- 也可以单独的定义数组的各个成员，也可以不使用连续的下标，并且下标的范围没有限制

  - 单独定义

    ```bash
    arr[0]=v1
    arr[1]=v2
    ```

  - 不连续使用

    ```
    arr[1]=v3
    arr[11]=v4
    ```

## 3.2 数组的读取

- 读取元素的一般格式为

  - ` ${数组名[下标] `
  - 使用 `@`符号，获取数组中的所有成员

- 获取数组长度的方法和获取字符串的方法相同

  ```bash
  # 数组定义
  arr1=(ada 1231 312,31,54,67,adsa ) # 数组成员之间用空格进行分割
  
  arr2[0]=11
  arr2[11]=1111
  
  # 获取元素
  echo ${arr1[0]} ${arr1[1]} # 按照下标获取 da 1231
  echo ${arr2[@]} # 获取所有的元素 11 1111
  
  # 获取长度
  # 获取单个
  echo ${#arr1[2]} # 17
  # 获取全部长度
  echo ${#arr1[@]} # 3
  echo ${#arr2[*]} # 2
  ```

# 四、注释的方法

- 单行注释 `#`

- 多行注释

  ```bash
  :<<EOF
  注释内容...
  注释内容...
  注释内容...
  EOF
  
  :<<'
  注释内容...
  注释内容...
  注释内容...
  '
  
  :<<!
  注释内容...
  注释内容...
  注释内容...
  !
  ```

  