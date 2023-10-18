- [相关文章](https://juejin.cn/post/6939892441436651527#heading-0)
  - grep 进行简单的文件筛选
  - awk 文本筛选，表格形态输出日志
    - 可以将复杂的 awk 语句写入脚本文件 `cal.awk`，然后通过 `-f` 选项指定从脚本文件执行



# 一、awk

- [link](https://blog.csdn.net/anqixiang/article/details/117903529?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522169561061616800184181316%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=169561061616800184181316&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_positive~default-2-117903529-null-null.142^v94^chatsearchT3_1&utm_term=awk&spm=1018.2226.3001.4187)

- 语法格式

  ```shell
  #  awk语法由一系列条件和动作组成，在花括号内可以有多个动作，多个动作之间用分号分隔，在多个条件和动作之间可以有若干空格，也可以没有
  awk [选项] ‘条件{动作}  条件{动作} ... ...’  文件名
  ```

- 指定分割符

  - `-F` 指定划分文件行之间的分隔符

- 正则表达式

  - 在 `/xxx/` 中间写正则表达式

  ```shell
  # 如果行中有xiaoju 则进行输出
  awk '/xiaoju/ {printf $0} \n' file.txt
  ```

  - `//` 全行数据正则匹配

  - `!//` 对全行数据正则匹配后取反

  - `~//` 对特定数据正则匹配

    ```shell
    # 对特定数据进行匹配时，~前面要带上特定数据
    # 比如下面再 ~ 之前的 $1 就是特定数据
        if ($1 ~ /.*/) {
          printf("拿到的配置：%s\n",$0)
        }
    ```

    

  - `!`//` 对特定数据正则匹配后取反

- 将匹配的值写入到变量

  - [link](https://stackoverflow.com/questions/6031612/assign-awk-result-to-variable)

  - 反引号之间包含任何 shell 命令，以将结果放入变量中

    ```shell
    RETURNCOUNT=`echo $INDEXCOUNT | awk '{print $3}'`
    ```

  - [[linux命令 $()代表什么意思](https://www.cnblogs.com/JYB2021/p/15011062.html)]([linux命令 $()代表什么意思](https://www.cnblogs.com/JYB2021/p/15011062.html))

    - 和反引号含义一致

    ```shell
    a=$(echo '111 222 33' | awk '{print $3;}' )
    echo $a # result is "33"
    ```

- 读取变量

  - awk 之中读取变量不能带引号

- 使用 shell命令

  - `system(命令) 函数`.可以直接在awk中调用shell命令，会启动一个新shell进程执行命令

