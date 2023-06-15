# 一、悲观锁和乐观锁

- [gd](https://dev.mysql.com/blog-archive/innodb-data-locking-part-2-locks/)

- [Mysql锁机制](https://blog.csdn.net/m0_45861545/article/details/121825980?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522168475200716800197019274%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=168475200716800197019274&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-2-121825980-null-null.142^v87^control_2,239^v2^insert_chatgpt&utm_term=mysql%20%E9%94%81%E6%9C%BA%E5%88%B6&spm=1018.2226.3001.4187)

  - 在**不通过索引条件查询的**时候，InnoDB使用的确实是表锁（锁的是整张表）！

  - 由于 MySQL 的行锁是针对索引加的锁,不是针对记录加的锁,所以虽然是访问不同行的记录,但是如果是使用相同的索引键,是会出现锁冲突的。

  - 当表有多个索引的时候,不同的事务可以使用不同的索引锁定不同的行,另外,不论 是使用主键索引、唯一索引或普通索引,InnoDB都会使用行锁来对数据加锁。

  - 即便在条件中使用了索引字段,但是否使用索引来检索数据是由 MySQL 通过判断不同 执行计划的代价来决定的如果 MySQL 认为全表扫效率更高,比如对一些很小的表,它 就不会使用索引,这种情况下 InnoDB 将使用表锁,而不是行锁。因此,在分析锁冲突时, 别忘了检查SQL 的执行计划（explain查看）,以确认是否真正使用了索引。

    

- [死锁](https://blog.csdn.net/sinat_27143551/article/details/103033205?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522168475257216800215060063%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=168475257216800215060063&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-2-103033205-null-null.142^v87^control_2,239^v2^insert_chatgpt&utm_term=mysql%20%E8%A1%8C%E9%94%81&spm=1018.2226.3001.4187)

- [link](https://blog.csdn.net/z_ssyy/article/details/128565101?ops_request_misc=%25257B%252522request%25255Fid%252522%25253A%252522168458067916800215097592%252522%25252C%252522scm%252522%25253A%25252220140713.130102334..%252522%25257D&request_id=168458067916800215097592&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-2-128565101-null-null.142%5Ev87%5Econtrol_2,239%5Ev2%5Einsert_chatgpt&utm_term=mysql%2520%25E4%25B9%2590%25E8%25A7%2582%25E9%2594%2581%25E5%2592%258C%25E6%2582%25B2%25E8%25A7%2582%25E9%2594%2581&spm=1018.2226.3001.4187)

  - 悲观锁
    - 当进行搜索时，如果明确主键(有唯一标识)，只会锁住当前的记录。如果没有明确主键，会导致整张表都被锁住
    - 取数据时都会直接加锁，共享资源每次只给一个线程使用，其它线程阻塞等待。在数据库中提供了行锁、表锁等，操作数据时先加锁后使用

  - 乐观锁
    - 不是数据库系统自带的，需要开发实现。乐观锁是只操作数据时并不进行任何特殊处理，也就是不加锁，在进行更新时才进行冲突判
    - [乐观锁的使用](https://blog.csdn.net/weixin_43692357/article/details/88708954?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522168474867116800215060654%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=168474867116800215060654&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-2-88708954-null-null.142^v87^control_2,239^v2^insert_chatgpt&utm_term=python%20%E5%AE%9E%E7%8E%B0%E4%B9%90%E8%A7%82%E9%94%81&spm=1018.2226.3001.4187)
  - 锁级别
    - 如果<font color=yellow> **明确** </font>的指出了主键，只会锁行。否则锁住的是整个表
    - 添加了（行级锁/表级锁）锁的数据不能被其他事务再锁定。也不能被其他事务修改

- MySQL中可以使用以下几种锁来控制并发访问：

  1. 共享锁（Shared Lock）：多个事务可以同时持有同一份资源的共享锁，但是不能持有排它锁。共享锁用于防止其他事务修改数据，但允许其他事务读取数据。

  2. 排它锁（Exclusive Lock）：一个事务持有排它锁时，其他事务不能持有排它锁或共享锁。排它锁用于防止其他事务读取或修改数据。

  3. 行锁（Row Lock）：锁定表中的一行数据，其他事务无法修改该行数据。行锁可以提高并发性能，但是也会增加锁的粒度。

  4. 为了给表加锁，可以使用以下两种方式：

     1. 使用 LOCK TABLES 语句：该语句可以锁定一个或多个表，可以使用共享锁或排它锁。

     1. 使用 SELECT ... FOR UPDATE 语句：该语句可以锁定查询结果集中的行，只能使用排它锁。

- MySQL是线程安全的，也就是说，多个线程可以同时访问同一个MySQL实例，而不会出现数据混乱或者崩溃的情况。但是，需要注意的是，如果多个线程同时对同一个数据对象进行修改操作，就可能会出现数据竞争的问题，需要使用锁机制来保证数据一致性和安全性。同时，在高并发环境下，还需要注意MySQL的配置和优化，以提高系统的性能和稳定性。

- Mysql 还有一致性非锁读和一致性锁读的问题

  - 如果是一致性非锁读，那么读取的数据不一定是可靠的

# 二、Mysql 在什么情况之下会加锁

- InnoDB 存储引擎在以下情况下会加锁：
  1. 事务中对数据进行修改时，InnoDB 会自动对修改的数据行加锁。如果不是使用 SELECT ... FOR UPDATE 或者 SELECT ... LOCK IN SHARE MODE 这两个语句，InnoDB 会自动使用排他锁（X 锁）来保护修改的数据行。
  2. 当多个事务同时对同一行数据进行修改时，InnoDB 会使用排他锁（X 锁）来保护数据行，防止并发修改导致数据不一致。
  3. 当事务对一个范围内的数据行进行修改时，InnoDB 会使用间隙锁（Gap Lock）来保护范围内的数据行，防止其他事务插入新的数据行导致数据不一致。
  4. 当使用 SELECT ... FOR UPDATE 或者 SELECT ... LOCK IN SHARE MODE 这两个语句时，InnoDB 会根据语句中的锁定模式（排他锁或共享锁）对数据行进行加锁
  5. 需要注意的是，InnoDB 会自动为事务中的操作加锁，但是如果事务中的操作不当，可能会导致锁的粒度过大或者死锁等问题。因此，在使用 InnoDB 存储引擎时，需要合理设计事务，避免出现锁的问题。
- MySQL 中会在以下情况下加锁：
  1. 当一个事务正在修改某个数据时，其他事务不能同时修改该数据，会对该数据加写锁。
  2. 当一个事务正在读取某个数据时，其他事务不能对该数据进行修改，但可以读取该数据，会对该数据加读锁。
  3. 当使用 SELECT ... FOR UPDATE 语句查询数据时，会对查询结果加写锁，防止其他事务同时修改查询结果。
  4. 当使用 LOCK TABLES 语句锁定表时，会对该表加表级别的写锁，其他事务不能对该表进行读写操作。
  5. 当使用事务级别为 SERIALIZABLE 时，会对所有查询涉及到的数据加共享锁。
  6. 当使用事务级别为 REPEATABLE READ 时，会对每个查询涉及到的数据加共享锁，直到事务结束。
  7. 当使用事务级别为 READ COMMITTED 时，会对每个查询涉及到的数据加共享锁，但不会持有锁到事务结束

# 三、一致性读取

## 3.1 一致性非锁读定

- <font color=yellow>一致性非锁定读的目的是为了效率</font>

- 对于多版本并发控制(MVCC)，读取快照数据时，可能数据库已经更新了，但是读取的还是陈旧的数据。当有需求读取数据库中稳定的数据时，可以采用一致性锁定读
- update操作会添加x锁。读提交、可重复读会使用一致性非锁定读。因此，在可重复读的情况之下，会导致数据库更新了，但是读出来的数据还是老的

## 3.2 一致性锁定读

- `select ... for update`  对读取的记录加一个x锁。 其它的事务连读都不能读了

- `select ... lock in share mode ` 对读取的记录加一个s锁，其它的事务可以继续读，但是不能写

- <font color=yellow>需要注意的是，即使加了这些锁，如果没有采用这种方式的`select ...` 也是可以使用快照读取的 </font>

  

## 3.3 使用Django验证锁的一致性读

- 非一致性读

  ```python
  def test24(request):
    with transaction.atomic():
      data = Book.objects.all()[0]
      data.num = 9999
      data.save(update_fields=['num'])
      time.sleep(2)
    return HttpResponse('Success!!!')
  
  
  def test26(request):
    with transaction.atomic():
      data = Book.objects.all()[0]
      print('26:', data.num)
    return HttpResponse('Success!!!')
  
  # 先运行24， 再运行26
  # 24事务的update操作不会导致26阻塞掉
  ```

- 一致性读

  ```python
  def test24(request):
    with transaction.atomic():
      data = Book.objects.select_for_update().filter(id=1)[0]
      data.num = 9999
      data.save(update_fields=['num'])
      time.sleep(2)
    return HttpResponse('Success!!!')
  
  
  def test26(request):
    with transaction.atomic():
      data = Book.objects.select_for_update().all()[0] # 1
      #data = Book.objects.all()[0]                    # 2
      print('26:', data.num)
    return HttpResponse('Success!!!')
  
  # 先运行24， 再运行26
  	# 如果26使用第一行 24事务的update操作会导致26阻塞掉
    # 如果26使用第二行 24事务的update操作不会导致26阻塞掉(这是因为这里使用的是快照读)
  
  ```

  

# 四、Django演示乐观锁

- [乐观锁、悲观锁Django使用参考链接](https://juejin.cn/post/6844903957526282254)
