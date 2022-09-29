- 当批量更新时，update_time 字段不会生效，也就是不会自动更新。因为这并没有经过python层进行映射，而是直接操作数据库。解决办法如下

  ```python
  import datetime
  
  xxx.update_time = datetime.datetime.now()
  ```

  