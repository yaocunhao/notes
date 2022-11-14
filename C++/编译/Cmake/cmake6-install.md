# 一、命令行基本介绍

- CMake 提供了一个命令行来安装已经生成的项目二叉树

  ```cmake
  cmake --install <dir> [<options>]
  ```

- 选项有

  - **--install** <dir>：必须选项，安装二进制的目录
  - **--config** <cfg>：必须选项， 安装的项目目录
  - **--component** <comp>：基于组件的安装
  - **--default-directory-permissions**：设置安装目录的权限，格式为`<u=rwx,g=rx,o=rx>`
  - **--prefix** <prefix>：添加安装的前缀
  - **--strip**：在安装前跳过
  - **-v**, **--verbose**： 是否输出详细信息



# 二、脚本运行

- 语法

  ```cmake
  install([[SCRIPT <file>] [CODE <code>]]
          [COMPONENT <component>] [EXCLUDE_FROM_ALL] [...])
          
          
  1. SCRIPT参数：在安装过程中调用给定的CMake脚本文件(即.cmake脚本文件)
                如果脚本文件名是相对路径，则将相对于当前源目录进行解释；
     CODE参数：在安装过程中调用给定的CMake代码；
              将代码指定为双引号字符串内的单个参数；
  2. 使用案例
     install(CODE "MESSAGE(\"Sample install message.\")")     
  ```

  