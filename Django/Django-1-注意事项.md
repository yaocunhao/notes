- 当批量更新时，update_time 字段不会生效，也就是不会自动更新。因为这并没有经过python层进行映射，而是直接操作数据库。解决办法如下

  ```python
  import datetime
  
  xxx.update_time = datetime.datetime.now()
  ```

- Django 默认会为每个请求创建一个数据库链接，当短时间内很多高并发请求时，这种建立连接的开销是比较大的，可以通过设置[`CONN_MAX_AGE`](https://docs.djangoproject.com/en/3.2/ref/settings/#std-setting-CONN_MAX_AGE)来解决。 数据库连接的生存期，以秒为单位。使用0在每个请求结束时关闭数据库连接，使用None表示无限制的持久连接