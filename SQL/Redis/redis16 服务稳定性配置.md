# 一、redis常见的问题

- [参考链接](https://blog.csdn.net/qq_41432730/article/details/121514758?spm=1001.2101.3001.6661.1&utm_medium=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-121514758-blog-126489667.pc_relevant_3mothn_strategy_and_data_recovery&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-121514758-blog-126489667.pc_relevant_3mothn_strategy_and_data_recovery&utm_relevant_index=1)
- [参考链接](https://blog.csdn.net/web17886480312/article/details/126489667?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522167323643116800225524112%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fall.%2522%257D&request_id=167323643116800225524112&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~pc_rank_34-3-126489667-null-null.142^v70^pc_new_rank,201^v4^add_ask&utm_term=redis%E6%9C%8D%E5%8A%A1%E7%BB%8F%E5%B8%B8%E5%B4%A9%E6%8E%89%EF%BC%8C%E5%A6%82%E4%BD%95%E6%9F%A5%E7%9C%8B%E5%8E%9F%E5%9B%A0&spm=1018.2226.3001.4187)
- [缓存指标管理]( https://blog.csdn.net/m0_72088858/article/details/126968918?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522167333214216800192276236%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fall.%2522%257D&request_id=167333214216800192276236&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~pc_rank_34-2-126968918-null-null.142^v70^pc_new_rank,201^v4^add_ask&utm_term=redis%20%E5%8F%AF%E4%BB%A5%E4%BD%BF%E7%94%A8%E7%9A%84%E5%86%85%E5%AD%98&spm=1018.2226.3001.4187)

## 1.1 缓存雪崩

- 缓存雪崩就是 Redis 的大量热点数据同时过期（失效），因为设置了相同的过期时间，刚好这个时候 Redis 请求的并发量又很大，就会导致所有的请求落到数据库

## 1.2 缓存穿透

## 1.3 数据一致性

