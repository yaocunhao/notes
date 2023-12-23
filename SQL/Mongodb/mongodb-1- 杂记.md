- [mongodb 不是所有参数都数有效可以设置的](https://forums.percona.com/t/how-to-set-cursortimeoutmillis/7534)
  
  - 可以在启动时进行设置` --setParameter cursorTimeoutMillis=10000 --setParameter globalConnPoolIdleTimeoutMinutes=1 --setParameter shardedConnPoolIdleTimeoutMinutes=1`
  
- [查看参数](https://blog.csdn.net/firstendhappy/article/details/106840081?ops_request_misc=&request_id=&biz_id=102&utm_term=mongodb%20%E6%B8%B8%E6%A0%87%E5%A4%B1%E6%95%88&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-5-106840081.142^v96^pc_search_result_base8&spm=1018.2226.3001.4187)
  
  ```
  use admin
  db.runCommand({getParameter:'*'})
  ```
  
  

- [多数据库](https://blog.csdn.net/qq_38923792/article/details/100182474?utm_medium=distribute.pc_relevant.none-task-blog-2~default~baidujs_utm_term~default-0-100182474-blog-119356028.235^v39^pc_relevant_3m_sort_dl_base1&spm=1001.2101.3001.4242.1&utm_relevant_index=3)
- [多数据库2](https://pymongo.readthedocs.io/en/stable/examples/high_availability.html)
  - Do **not** connect PyMongo to a pool of mongos instances through a load balancer. A single socket connection must always be routed to the same mongos instance for proper cursor support.
- [[mongod和mongos之间的确切区别是什么？](https://qa.1r1g.com/sf/ask/1985534491/)]
- [Pymongo 官网](https://pymongo.readthedocs.io/en/stable/tutorial.html)
- [分片集群搭建](https://blog.csdn.net/lxhsjl121/article/details/130413441?utm_medium=distribute.pc_relevant.none-task-blog-2~default~baidujs_utm_term~default-1-130413441-blog-107782464.235^v39^pc_relevant_3m_sort_dl_base1&spm=1001.2101.3001.4242.2&utm_relevant_index=4)