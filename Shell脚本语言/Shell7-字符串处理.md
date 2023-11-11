- [link](https://blog.csdn.net/jiang0615csdn/article/details/124660710?ops_request_misc=&request_id=&biz_id=102&utm_term=shell%20%E8%84%9A%E6%9C%AC%E5%A4%84%E7%90%86%E5%AD%97%E7%AC%A6%E4%B8%B2&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-1-124660710.142^v96^pc_search_result_base1&spm=1018.2226.3001.4187)

- 字符串分割替换

  - 空格是分割成数组
  - 其它的是进行替换，最后得到字符串

  ```shell
  #!/bin/bash
  
  string="hello,shell,split,test"
  array=(${string//,/ }) 
  
  for var in ${array[@]}
  do
     echo "---"
     echo $var
  done
  ```

- 字符串截取

  - 使用#号可以截取指定字符（或者子字符串）右边的所有字符

    ```bash
    格式：
    ${parameter#word}   # 删除匹配前缀
    ${parameter##word}  # 最长匹配模式，删除匹配前缀(也可以理解从右往左匹配的第一个值)
    ${parameter%word}   # 删除匹配后缀
    ${parameter%%word}
    
    # 如果不带 * 则表示需要以某个单词开头
    # 如果有 *， 则可以在任意地方
    ```

  - 使用 % 从后往前删

    ```shell
    url=123word456word789
    echo ${url%word*} # '#' 删除的*放在前面， %匹配删除则是放在后面
    
    cur_path=$PWD
    echo $cur_path
    echo ${cur_path%voyager*}
    echo ${cur_path#*voyager}
    
    # --------- 测试 ---------
    cur_path=123www456
    echo $cur_path
    echo ${cur_path%www*}
    echo ${cur_path#*www}
    
    123www456
    123
    456
    ```

    

- 变量状态赋值

  ```sh
   ${VAR:-string}  如果 VAR 变量为空则返回 string
  
  ${VAR:+string}  如果 VAR 变量不为空则返回 string
  
  ${VAR:=string}  如果 VAR 变量为空则重新赋值 VAR 变量值为 string
  
  ${VAR:?string}  如果 VAR 变量为空则将 string 输出到 stderr 
  ```

  