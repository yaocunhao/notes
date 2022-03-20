[文章连接](https://blog.csdn.net/weixin_43288201/article/details/105643692?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522164672904316780271518406%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fall.%2522%257D&request_id=164672904316780271518406&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~rank_v31_ecpm-1-105643692.pc_search_result_cache&utm_term=shell%E8%84%9A%E6%9C%AC%E8%AF%AD%E8%A8%80&spm=1018.2226.3001.4187)



# 一、三种不同的执行方式

- ./xxx.sh :先按照 文件中#!指定的解析器解析，#！不存在再使用默认的解释器，下同
- bash xxx.sh:指明先用bash解析器解析
- . xxx.sh 直接使用默认解析器解析（不会执行第一行的#！指定的解析器）但是第一行还是要写的

# 二、变量

- 定义变量：变量名=变量值，num=10
- 引用变量：$变量名
- 清除变量值：unset