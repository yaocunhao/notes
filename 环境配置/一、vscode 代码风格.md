# 一、vscode 代码风格

- 使用yapf格式化插件，然后自定义风格

- `yapf  --style-help   `导出配置文件内容至`.style.yapf`。然后进行settings.json 之中进行如下配置即可

  ```python
  {
    "python.defaultInterpreterPath": "/home/didi/anaconda3/envs/my_python/bin/python",
    "python.formatting.provider": "yapf",
    "python.formatting.yapfArgs": [
      "--style",
      "/home/didi/test/.style.yapf" # 设置代码风格
    ],
    "json.schemas": []
  }
  ```

  

# 二、vscode安装的插件

- [找不到定义插件](https://github.com/microsoft/vscode/issues/96754)
  - vscode-msr




# 三、配置C++环境

- [参考链接](https://blog.csdn.net/reachzh1/article/details/120413575?ops_request_misc=&request_id=&biz_id=102&utm_term=mac%20vscode%E9%85%8D%E7%BD%AEc++%E7%8E%AF%E5%A2%83&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-1-120413575.142^v41^pc_rank_34_1,185^v2^control&spm=1018.2226.3001.4187)



# 四、vscode 配置文件的作用

- `lanuch.json` 运行任务设置
  - 主要是配置可运行文件的位置所在
  - 对于脚本语言，比如python就可以直接使用该配置文件即可

- `tasks.json`编译的文件夹
  - 对于编译型语言，在运行前需要进行编译，采用的就是这个文件之中配置的方法

- settings.sjon



# 五、Vscode 编译器预设变量

- [官网](https://code.visualstudio.com/docs/editor/variables-reference)

```python
${workspaceFolder} - VS Code 中打开的文件夹目录(也就是项目的根目录)
${workspaceFolderBasename} - 没有任何斜杠 (/)的 VS Code 中打开的文件夹目录
${file} - 目前打开文件的绝对位置
${relativeFile} - 目前打开文件相对于 workspaceFolder 的相对位置
${fileBasename} -  目前打开文件的文件名（有拓展名，如： main.cpp）
${fileBasenameNoExtension} - 目前打开文件的出去拓展名的文件名（无拓展名， 如： main.cpp）
${cwd} - task runner的工作目录
${fileDirname} - 目前打开文件的目录位置
${fileExtname} - 目前打开文件的拓展名
${lineNumber} - 文件中目前被选择的行数
${selectedText} - 文件中目前被选择的内容
```



# 六、插件

- [插件](https://hackr.io/blog/best-vscode-extensions)
  - 正则表达式插件
