# 一、如何查看占用缓存资源

- redis-cli 连接reids服务器

- info memory 查看使用缓存

  ```shell
  used_memory 由redis分配起分配的内存总量（包括redis进程开销和数据占用的内存）
  used_memory_human 格式化显示使用内存量
  
  used_memory_rss (rss是redis set size的缩写）该进程占物理内存的大小，是操作系统分配给redis实例的内存大小
  used_memory_rss_human  格式化显示
  
  used_memory_peak  ： redis的内存消耗峰值
  used_memory_peak_human  格式化显示
  
  used_memory_peak_perc  使用内存达到峰值内存的百分比（used_memory/used_memory_peak）*100%
  
  used_memory_overhead :redis为了维护数据集的内部版机制所需的内存开销，包括所有客户段输出缓冲区/查询缓冲区，aop 重写缓冲区和主从复制的backlog
  
  used_memory_startup   redis服务起启动时消耗的内存
  used_memory_dataset    数据占用的内存大小，即used_memory-userd_memory_overhead
  used_memory_dataset_perc  数据占用内存大小的百分比  100%*（used_memory_dataset/used_memory-used_memory_startup）
  
  total_system_memroy  270232113152 整个系统内存
  total_system_memory_human  251.67G 格式化显示(上面显示的字节B，这里格式化显示成)
  
  used_memory_lua  lua 脚本存储占用内存
  used_memory_lua_human  格式化显示
  
  
  maxmemory  redis实例的最大内存配置
  maxmemory_human   格式化显示
  
  maxmemory_policy 当达到maxmemory时的淘汰策略
  mem_fragmentation_ratio  内存的碎片率 ，used_memory_rss/used_memory --4.0版本之后可以使用memory purge手动回收内存
  
  mem_allocator  内存分配器
  
  active_defrag_running  表示没有活动的defrag任务正在运行  1.表示有活动的defrag任务正在运行（defrag 表示内存碎片整理）
  
  lazyfree_pending_objects  表示redis执行lazy free操作 在等待被实际回收内容的键个数
  ```

- 或者在连接之后执行命令` config get key`也可查看对应key的值

  ```linux
  127.0.0.1:6379> config get maxmemory
  1) "maxmemory"
  2) "85899345920"
  ```