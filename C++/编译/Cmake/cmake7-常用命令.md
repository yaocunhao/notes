- cmake_parse_arguments(<prefix> <options> <one_value_keywords> <multi_value_keywords> ${ARGN})
  - **`<prefix>`**: 这是变量的前缀，用于存储解析后的参数值。这个前缀将添加到每个解析出的变量名的前面。
  - **`<options>`**: 这是一个列表，其中包含那些作为开关存在的选项（布尔值），这些选项没有值，它们要么被指定（为TRUE），要么未被指定（为FALSE）。
  - **`<one_value_keywords>`**: 这是一个关键字列表，每个关键字后面跟随一个值。
  - **`<multi_value_keywords>`**: 这是一个关键字列表，每个关键字后面可以跟随多个值。

    ```cmake
    macro(example_macro)
        cmake_parse_arguments(PREFIX
                              "OPTIONS"  # 布尔选项， 注意这里可以是一个列表
                              "KEY"  # 单值键
                              "MULTIKEY"  # 多值键
                              ${ARGN})
    
        # 使用解析后的参数
        if(PREFIX_OPTIONS)
            message("Option is enabled")
        endif()
    
        if(DEFINED PREFIX_KEY)
            message("Key value is: ${PREFIX_KEY}")
        endif()
    
        if(DEFINED PREFIX_MULTIKEY)
            message("Multikey values are: ${PREFIX_MULTIKEY}")
        endif()
        message("ARGS param:${ARGN}")
    endmacro()
    
    # 调用宏并传递参数
    message("第一次实验-------")
    example_macro(OPTIONS KEY "value" MULTIKEY "val1" "val2")
    message("第二次实验-------")
    example_macro(OPTIONS2 KEY "value" MULTIKEY "val1" "val2")
    message("第三次实验-------")
    example_macro("val1" "val2")
    message("第四次实验-------")
    example_macro(OPTIONS 1 2 3 4 KEY "value" MULTIKEY "val1" "val2")
    
    # '''''''''''''''''''运行结果''''''''''''''''''''''''''''''''
    
    第一次实验-------
    Option is enabled
    Key value is: value
    Multikey values are: val1;val2
    ARGS param:OPTIONS;KEY;value;MULTIKEY;val1;val2
    第二次实验-------
    Key value is: value
    Multikey values are: val1;val2
    ARGS param:OPTIONS2;KEY;value;MULTIKEY;val1;val2
    第三次实验-------
    ARGS param:val1;val2
    第四次实验-------
    Option is enabled
    Key value is: value
    Multikey values are: val1;val2
    ARGS param:OPTIONS;1;2;3;4;KEY;value;MULTIKEY;val1;val2
    
    # 总结
    # PREFIX 会作为前缀和 OPTIONS、 KEY、 MULTIEKY 组成对应的bool、单值、多值
    
    
    ```
  
    
  
- `get_filename_component` 函数用于操作路径和文件名。这个命令提取路径、文件名、扩展名等部分，或者计算相对路径。它非常灵活，可以用于多种不同的场景，从简单的文件名提取到复杂的路径操作

  ```cmake
  get_filename_component(<variable> <path> <component> [CACHE])
  
  <variable>: 存储结果的变量名。
  <path>: 要操作的完整路径字符串。
  <component>: 指定要提取的路径组件。常用的组件包括：
  PATH: 提取路径部分，不包括文件名。
  NAME: 提取文件名，不包括路径。
  EXT: 提取文件的扩展名。
  NAME_WE: 提取文件名，但不包括扩展名。
  ABSOLUTE: 转换为绝对路径。
  REALPATH: 转换为绝对路径，并解析所有符号链接。
  ```

  