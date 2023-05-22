# 一、Redis线程安全

- redis本身就是线程安全的，因此不管外接是如何来进行连接和获取的都是线程安全的

# 二、Mysql 线程安全

- 不是线程安全的，mysql本身就不是线程安全的，操作的时候需要进行加锁处理

- 可以直接使用Django 之中的事务方式来进行操作

  - ```
    with transaction.atomic(using=get_db_name(cls)):
    ```



# 三、Django本身线程安全问题

- [参考链接](https://blog.csdn.net/Coxhuang/article/details/89953619?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522167522410716800217094976%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fall.%2522%257D&request_id=167522410716800217094976&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~rank_v31_ecpm-5-89953619-null-null.142^v72^insert_down2,201^v4^add_ask&utm_term=Django%20%E6%98%AF%E5%8D%95%E7%BA%BF%E7%A8%8B%EF%BC%9F&spm=1018.2226.3001.4187)
- [如何处理安全问题](https://blog.csdn.net/m0_37714245/article/details/82027543?ops_request_misc=&request_id=&biz_id=102&utm_term=Django%20%E5%A6%82%E4%BD%95%E5%A4%84%E7%90%86%E5%B9%B6%E5%8F%91%E5%AE%89%E5%85%A8%E9%97%AE%E9%A2%98%EF%BC%9F&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-0-82027543.142^v87^control_2,239^v2^insert_chatgpt&spm=1018.2226.3001.4187)

