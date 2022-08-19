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

  

# 二、批量化操作带来的线程安全问题

- [参考链接1](https://blog.csdn.net/wdj_yyds/article/details/125374509?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522166056601116782246443540%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fall.%2522%257D&request_id=166056601116782246443540&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~pc_rank_34-1-125374509-null-null.142^v40^pc_rank_34_2,185^v2^control&utm_term=redis%20%E6%89%B9%E9%87%8F%E8%8E%B7%E5%8F%96%E7%9A%84%E6%95%88%E7%8E%87&spm=1018.2226.3001.4187)
- [解决办法](https://blog.csdn.net/chuimingwen0582/article/details/101021609?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522166056634916782350884479%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fall.%2522%257D&request_id=166056634916782350884479&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~pc_rank_34-15-101021609-null-null.142^v40^pc_rank_34_2,185^v2^control&utm_term=%E9%9B%86%E7%BE%A4%E6%A8%A1%E5%BC%8F%E4%B8%8B%E6%98%AF%E7%A6%81%E6%AD%A2%E6%89%A7%E8%A1%8C%E5%83%8Fmget%E3%80%81mset%E3%80%81pipeline%E7%AD%89%E6%89%B9%E9%87%8F%E6%93%8D%E4%BD%9C%E7%9A%84&spm=1018.2226.3001.4187)
