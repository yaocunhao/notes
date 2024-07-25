- [gd](https://bazel.build/reference/be/c-cpp?hl=zh-cn#cc_library)

- bazel 中， 使用 @//、 // 、 ： 开头是啥意思

  ```bash
  在 Bazel 中，@//、//和:是用于指定目标路径和名称的特殊符号，它们在构建文件中非常常见。下面是每个符号的具体含义和用法：
  
  // (双斜杠)
  用途: 表示一个绝对路径，从工作空间的根目录开始。
  示例: //projects/my_app指的是位于你的工作空间根目录下的projects/my_app目录。
  
  @ (at 符号)
  用途: 用于引用外部仓库或依赖。
  示例: @my_repo//projects/my_lib指的是名为my_repo的外部仓库中的projects/my_lib目录。
  
  : (冒号)
  用途: 用于分隔目录路径和目标名称。它跟在目录路径后面，指定该目录下的具体目标（如规则或文件名）。
  示例: //projects/my_app:my_lib指的是projects/my_app目录下名为my_lib的目标。
  
  Bazel 的依赖路径中，冒号用于分隔包路径和目标名称。如果目标和引用它的 BUILD 文件位于同一个包中，则可以省略包路径，只使用:和目标名称。例如：
  
  :ccc_of21 表示当前包下名为 ccc_of21 的目标。
  :cccc_pod_ss 表示当前包下名为 cccc_pod_ss 的目标。
  
  ```

- bazel proto_libary 中 strip_import_prefix指定的是文件相对导入时的路径，相当于cmakelist的target_include_directories。 文件的实际导入位置还是以当前BUILD.bazel 为相对路径的


