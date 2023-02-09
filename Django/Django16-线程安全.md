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
- 本身是线程安全的，，但是添加uwsgi和nginx后就不是了
