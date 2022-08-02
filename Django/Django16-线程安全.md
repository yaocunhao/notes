# 一、Redis线程安全

- redis本身就是线程安全的，因此不管外接是如何来进行连接和获取的都是线程安全的

# 二、Mysql 线程安全

- 不是线程安全的，mysql本身就不是线程安全的，操作的时候需要进行加锁处理

- 可以直接使用Django 之中的事务方式来进行操作

  - ```
    with transaction.atomic(using=get_db_name(cls)):
    ```

