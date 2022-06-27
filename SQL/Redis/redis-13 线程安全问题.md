# 一、Redis是否是线程安全的

- redis本身是线程安全的

- 如果出现并发问题，**不是redis问题，而是业务逻辑的问题**

  ```python
  import redis
  from concurrent import futures
  import time
  
  r = redis.Redis(host='127.0.0.1', port=6379, db=0, password=8888)
  r2 = redis.Redis(host='127.0.0.1', port=6379, db=0, password=8888)
  l = []
  
  # 两个客户端，对数据进行写入，最后得到的值没有出现错误
  def work(num):
    if (num % 2 != 0):
      time.sleep(0.1)
      r.set(str(num), num)
    else:
      time.sleep(0.1)
      r2.set(str(num), num)
  
  
  def work2(num):
    l.append(int(r.get(str(num))))
  
  
  with futures.ThreadPoolExecutor(20) as executor:
    for i in range(300):
      executor.submit(work2, i)
  
  l.sort()
  print(l)
  ```

  

