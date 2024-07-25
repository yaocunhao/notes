- [参考链接](https://zhuanlan.zhihu.com/p/492932151)
- [gd](https://cmake.org/cmake/help/latest/guide/tutorial/index.html)

# 一、是什么

- cmake可以根据CMakeLists.txt生成Makefile
- CMakeList语法简单，面对大型项目时比直接编写makefile效率更高

# 二、接口

- cmake_minimum_required(VERSION 2.6)

  - cmake最低版本需求，不加入此行会受到警告信息

- project(Tutorial VERSION 1.0)

  - 指定当前项目名称和版本号

- set(CMAKE_CXX_STANDARD 11)

  - 用于定义变量

- add_library

  - 根据源码生成一个库

    ```cmake
    # name： 逻辑名称，在项目之中必须是唯一存在的，完整的库名依赖于具体的构建方式
    # 构建方式： 静态、共享、
    # 从哪里构建
    add_library(<name> [STATIC | SHARED | MODULE]
                [EXCLUDE_FROM_ALL]
                [<source>...])
    
    ```

- add_subdirectory
  - 这个命令可以**向当前工程添加存放源文件的子目录**，并可以指定中间二进制和目标二进制的存放位置
  - add_subdirectory(source_dir [binary_dir] [EXCLUDE_FROM_ALL])
    - source_dir指定一个目录，其中存放CMakeLists.txt文件和代码文件。**指定子目录代码文件和cmakelist文件的存放文职**
    - binary_dir指定的目录存放输出文件，如果没有指定则使用source_dir。 **指定子目录cmake和make后生成的二进制和可运行文件的存放位置**
    - 外层的CMakeLists.txt用于掌控全局，**使用add_subdirectory来控制其它目录下的CMakeLists.txt的运行**
  
- add_executable

  - 使用**指定的源文件**来**生成**目标**可执行文件**。具体分为三类：普通、导入、别名

  - 其中<name>是可执行文件的名称，在cmake工程中必须唯一

  - WIN32用于在windows下创建一个以WinMain为入口的可执行文件。MACOSX_BUNDLE用于mac系统或者IOS系统下创建一个GUI可执行应用程序

    ```cmake
    
    # 由source列出的文件构建而来
    # 生成的可执行程序名字为name
    # 第二个环境选择参数可以省略
    # EXCLUDE_FROM_ALL:用于指定可执行目标是否会被构建，当该选项使用的时候，可执行目标不会被构建
    # source1：构建可执行目标文件所需要的源文件，也可以通过target_sources()继续为可执行目标文件添加源文件，要求是在调用target_sources之前，可执行目标文件必须已经通过add_executable或add_library定义了
    add_executable(<name>  # 生成的文件名称
    							 [WIN32] [MACOSX_BUNDLE] # 选择编译的环境
                   [EXCLUDE_FROM_ALL] # 指定目标是否被构建
                   [source1] [source2 ...] # 源文件列表
                   )
                   
    
    # 1、基础用法： 根据文件生成可执行程序
    add_executable(hello_cmake main.cpp)
    
    
    
    # 2、EXCLUDE_FROM_ALL 参数用法
    add_executable(test EXCLUDE_FROM_ALL test.cpp)
    # 在编译的时候test不会被编译，如果需要编译需要手动编译, 比如make test
    
    
    
    # 3、导入可执行目标文件
    add_executable (<name> IMPORTED [GLOBAL])
    # 将工程外部的可执行目标文件导入进来，不会有任何构建可执行目标文件的动作发生
    # name:导入可执行文件目标的名字
    # GLOBAL：可执行目标文件的范围为文件创建的目录及子目录；指定GLOBAL则会将范围扩大到整个工程
    # IMPORTED：选项指定后，属性IMPORTED会被置为TRUE，在工程内构建的可执行z目标文件的属性IMPORTED会被置为FALSE
    
    
    
    # 4、别名可执行文件
    # add_executable (<name> ALIAS <target>)
    # 为可执行目标文件创建一个别名。创建该别名后，可以使用别名进行可执行目标的读、测试操作，但是不能利用别名对可执行目标的修改属性操作
    # 别名不会在make之后出现，而是作用于后续的makelist 之中
    
    
    ```

- target_link_libraries

  - 指定链接给定目标和/或其依赖项时要使用的库。命名的<目标>必须是由add_executable()或add_library()之类的命令创建的，并且不能是ALIAS目标

    ```cmake
    target_link_libraries(<target>
                          <PRIVATE|PUBLIC|INTERFACE> <item>...
                         [<PRIVATE|PUBLIC|INTERFACE> <item>...]...)
    ```

- include_directories

  - 给头文件添加"前缀路径"

  - [AFTER|BEFORE]定义了追加指定目录的方式在头还是尾。[SYSTEM]告诉编译器在一些平台上指定目录被当作系统头文件目录
  
    ```cmake
    include_directories([AFTER|BEFORE] [SYSTEM] dir1 [dir2 ...])
    ```

    ![image-20221031205449928](https://yrecord.oss-cn-hangzhou.aliyuncs.com/picture/202210312054790.png)
  
  - 找头文件有用，找源文件的话还是需要相对于CMAKE_CURRENT_SOURCE_DIR
  
- include

  - 从指定的文件加载、运行CMake代码。如果指定文件，则直接处理。如果指定module，则寻找module.cmake文件，首先在${CMAKE_MODULE_PATH}中寻找，然后在CMake的module目录中查找

    ```cmake
    include(<file|module> [OPTIONAL] [RESULT_VARIABLE <VAR>]
                          [NO_POLICY_SCOPE])
    ```

- target_include_directories

  - 在编译目标文件<target>时指定头文件。<target>必须是通过add_executable()或add_library()创建，且不能是ALIAS目标。<INTERFACE|PUBLIC|PRIVATE>修饰其紧跟参数items的作用范围

    ```cmake
    target_include_directories(<target> [SYSTEM] [AFTER|BEFORE]
      <INTERFACE|PUBLIC|PRIVATE> [items1...]
      [<INTERFACE|PUBLIC|PRIVATE> [items2...] ...])
    ```

- link_directories

  - 为链接器添加库的搜索路径，此命令调用之后生成的目标才能生效。link_directories()要放在add_executable()之前

    ```cmake
    link_directories([AFTER|BEFORE] directory1 [directory2 ...])
    ```

- find_package

  - **CMake 的 find_package 命令用于查找并加载指定的第三方库**。使用 find_package 命令可以在构建项目时自动查找第三方库的安装目录，并将它们加入编译器和链接器的搜索路径中

  - [link](https://wenku.baidu.com/view/342e69a1270c844769eae009581b6bd97f19bcef.html?_wkts_=1690248690798)


  ```protobuf
  
  CMake 的 find_package 命令用于查找并加载指定的第三方库。使用 find_package 命令可以在构建项目时自动查找第三方库的安装目录，并将它们加入编译器和链接器的搜索路径中。例如：
  
  
  find_package(Boost REQUIRED)
  include_directories(${Boost_INCLUDE_DIRS})
  target_link_libraries(myTarget ${Boost_LIBRARIES})
  find_package 命令需要指定查找的库名称，可选的还有 REQUIRED, QUIET, NO_MODULE等参数。
  ```

  - 共支持两种模式

    ```cmake
     mode1: Module, 此模式需访问Find<PackageName>.cmake文件
    find_package(<PackageName> [version] [EXACT] [QUIET] [MODULE]
                 [REQUIRED] [[COMPONENTS] [components...]]
                 [OPTIONAL_COMPONENTS components...]
                 [NO_POLICY_SCOPE])
    
    # mode2: Config, 此模式需访问<lowercasePackageName>-config.cmake or <PackageName>Config.cmake
    find_package(<PackageName> [version] [EXACT] [QUIET]
                 [REQUIRED] [[COMPONENTS] [components...]]
                 [OPTIONAL_COMPONENTS components...]
                 [CONFIG|NO_MODULE]
                 [NO_POLICY_SCOPE]
                 [NAMES name1 [name2 ...]]
                 [CONFIGS config1 [config2 ...]]
                 [HINTS path1 [path2 ... ]]
                 [PATHS path1 [path2 ... ]]
                 [PATH_SUFFIXES suffix1 [suffix2 ...]]
                 [NO_DEFAULT_PATH]
                 [NO_PACKAGE_ROOT_PATH]
                 [NO_CMAKE_PATH]
                 [NO_CMAKE_ENVIRONMENT_PATH]
                 [NO_SYSTEM_ENVIRONMENT_PATH]
                 [NO_CMAKE_PACKAGE_REGISTRY]
                 [NO_CMAKE_BUILDS_PATH] # Deprecated; does nothing.
                 [NO_CMAKE_SYSTEM_PATH]
                 [NO_CMAKE_SYSTEM_PACKAGE_REGISTRY]
                 [CMAKE_FIND_ROOT_PATH_BOTH |
                  ONLY_CMAKE_FIND_ROOT_PATH |
                  NO_CMAKE_FIND_ROOT_PATH])
    find_package一般用于加载外部库到项目中，并且会加载库的细节信息。如上find_package有两种模式：Module与Config
    ```

    

- 环境变量
  - EXECUTABLE_OUT_PATH和PROJECT_SOURCE_DIR是CMake自带的预定义变量，其意义如下
    - EXECUTABLE_OUTPUT_PATH ：目标二进制可执行文件的存放位置
    - PROJECT_SOURCE_DIR：工程的根目录,也是库文件的默认输出位置(就是第一个cmakelists 所在的目录)
    - PROJECT_BINARY_DIR:  二进制文件目录(执行cmake时，所在目录)

- execute_process
  - 执行shell脚本

- aux_source_directory(. DIR_SRCS)
  - 遍历当前目录的所有文件并使用DIR_SRCS保存
  
- 