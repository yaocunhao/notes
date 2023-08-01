- [gd](https://cmake.org/cmake/help/latest/manual/cmake-language.7.html#cmake-language-variables)
- [中文介绍](https://blog.csdn.net/weixin_43708622/article/details/108315184?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522166799014016782390580051%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=166799014016782390580051&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~baidu_landing_v2~default-1-108315184-null-null.142^v63^wechat,201^v3^control_1,213^v2^t3_esquery_v2&utm_term=cmake%20Directory%20Scope&spm=1018.2226.3001.4187)
- [环境变量](https://cmake.org/cmake/help/latest/manual/cmake-variables.7.html)

# 一、基本介绍

- 变量是cmake的基本存储单位，通过`set()`和`unset()`来进行设置和取消
- 变量有大小写之分，通常用字符和`_`进行组合来表示一个变量名

# 二、Cmake变量的作用域

## 2.1 函数作用域

- 在函数中间定义的变量的作用域在函数之中

## 2.2 目录作用域

- 子目录会传值拷贝父目录的所有变量，所以在子目录之中进行修改拷贝下来的变量不会改变父目录中原来变量的值
- 不在函数中通过`set()`定义的变量的作用域就在目录作用域之中

## 2.3 缓存作用域

- 缓存变量在整个cmake工程的编译生命周期内都有效，工程内的其他任意目录都可以访问缓存变量
- 通过`set`的cache选项进行设置

## 2.4 变量的查找顺序

- 先在函数作用域之中寻找，然后是目录作用域，然后是缓存作用域
- `$CACHE{VAR}` 可以直接从缓存作用域之中读取内容

# 三、Cmake和环境变量

- 环境变量在CMake流程中具有全局作用域。它们从不缓存
- 变量引用的形式为$ENV{<变量>}
- CMake环境变量的初始值是调用进程的初始值。可以使用set()和unset()命令更改值。这些命令只影响正在运行的CMake进程，而不影响整个系统环境。更改后的值不会写回调用流程，后续的构建或测试流程也不会看到它们



