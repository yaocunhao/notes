[参考链接](https://blog.csdn.net/weixin_43702146/article/details/120701988?spm=1001.2101.3001.6661.1&utm_medium=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7ECTRLIST%7Edefault-1-120701988-blog-125084872.pc_relevant_multi_platform_whitelistv1&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7ECTRLIST%7Edefault-1-120701988-blog-125084872.pc_relevant_multi_platform_whitelistv1&utm_relevant_index=1)





- [docker-built](https://blog.csdn.net/taiyangdao/article/details/71718805?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522165690291116781685394246%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=165690291116781685394246&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~baidu_landing_v2~default-1-71718805-null-null.142^v30^pc_rank_34,185^v2^control&utm_term=docker+built+-t&spm=1018.2226.3001.4187)
  - [docker](https://so.csdn.net/so/search?q=docker&spm=1001.2101.3001.7020) build命令用于根据给定的Dockerfile和上下文以构建Docker镜像
  - **--tag, -t，镜像的名字及tag**

- [查看docker的用户信息](https://www.cnblogs.com/yinguohai/p/13520005.html)

- 将主机文件复制到docker
  - docker cp `主机文件路径` `容器id:容器内路径 `
- 从docker容器内复制文件到主机

  - docker cp `容器id:容器内文件路径` `主机路径`

- docker 拷贝打包镜像
  - docker save 镜像名称:镜像版本、镜像名称2:镜像版本2  |  gzip >  `name.tgz`
- docker 导入镜像
  - docker load -i name  
- uwsgi 重启
  - uwsgi --reload /dev/shm/uwsgi.pid 

- [批量删除](https://blog.csdn.net/WuLex/article/details/120257730?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522166019016016781683955094%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=166019016016781683955094&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-1-120257730-null-null.142^v40^pc_rank_34_2,185^v2^control&utm_term=docker%20%E6%89%B9%E9%87%8F%E5%88%A0%E9%99%A4%E5%AE%B9%E5%99%A8&spm=1018.2226.3001.4187)

- [查看容量](https://blog.csdn.net/weixin_43066097/article/details/116410785?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522169512202816800227452653%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=169512202816800227452653&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-2-116410785-null-null.142^v94^chatsearchT3_1&utm_term=docker%20%E5%8D%A0%E7%94%A8%E7%A9%BA%E9%97%B4%E8%BF%87%E5%A4%A7&spm=1018.2226.3001.4187)
  - docker system df
- [空间清理](https://blog.csdn.net/pushiqiang/article/details/105991522?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522169512234016800197087025%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=169512234016800197087025&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-4-105991522-null-null.142^v94^chatsearchT3_1&utm_term=Local%20Volumes&spm=1018.2226.3001.4187)
  - docker system prune 
- [Dockerfile 一些编写规则](https://blog.csdn.net/xcbeyond/article/details/124917942?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522169512598716800225565363%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fall.%2522%257D&request_id=169512598716800225565363&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~rank_v31_ecpm-1-124917942-null-null.142^v94^chatsearchT3_1&utm_term=Dockerfile%20%E4%B9%8B%E4%B8%AD%20RUN%20%E8%B6%8A%E5%B0%91%E8%B6%8A%E5%A5%BD%E4%B9%88&spm=1018.2226.3001.4187)