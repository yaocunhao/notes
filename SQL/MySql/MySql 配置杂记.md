- 参考链接
  - [配置文件含义](https://blog.csdn.net/u010520724/article/details/107456122?ops_request_misc=&request_id=&biz_id=102&utm_term=mysql%20%E9%85%8D%E7%BD%AE%E6%96%87%E4%BB%B6%E9%82%A3%E4%B9%88%E5%A4%9A%E6%98%AF%E5%93%AA%E4%B8%80%E4%B8%AA%EF%BC%9F&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-0-107456122.142^v10^control,157^v4^control&spm=1018.2226.3001.4187)
  - [身份验证插件](https://blog.csdn.net/SunZLong/article/details/103926902)
- 本地连接
  - mysql -h localhost -u root -p123 
    - -p 为密码，-p和密码之间**不可有空格**
- 远程连接
  - mysql -h -P -u -p
    - -P为端口，-p为密码
  
- 文件的存储
  - [文件存储位置解释链接](https://blog.csdn.net/weixin_43395911/article/details/122634903?ops_request_misc=&request_id=&biz_id=102&utm_term=mysql%20%E7%9A%84%E6%95%B0%E6%8D%AE%E5%AD%98%E5%9C%A8%E5%93%AA%E9%87%8C&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-1-122634903.142^v10^control,157^v4^control&spm=1018.2226.3001.4187)

- 查询、关闭、开启
  - 查询：service mysql status
  - 关闭：service mysql stop
  - 开启：service mysql start

- ubuntu中配置文件的位置
  - /etc/mysql/mysql.conf.



# 安装使用

[参考链接](https://blog.csdn.net/qq_37598011/article/details/93489404?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522165339277516782395390001%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fall.%2522%257D&request_id=165339277516782395390001&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~hot_rank-1-93489404-null-null.142^v10^pc_search_result_control_group,157^v12^control&utm_term=linux+%E4%B8%8B%E5%AE%89%E8%A3%85mysql&spm=1018.2226.3001.4187)

[找不到文件](https://blog.csdn.net/weixin_34365635/article/details/93424083)

- 启动命令

  ```python
  关闭mysql 
  mysqladmin -uroot -p shutdown 
  启动mysql 
  sudo mysqld --defaults-file=/etc/my.cnf --user=root
  ```