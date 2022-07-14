[基本概念连接](https://blog.csdn.net/pangjunwei/article/details/106901927?spm=1001.2101.3001.6661.1&utm_medium=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-106901927-blog-81094378.pc_relevant_antiscanv2&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-106901927-blog-81094378.pc_relevant_antiscanv2&utm_relevant_index=1)

- 创建一个用户，用户的权限对应的是数据库

- 用户验证 db.auth('user','passwd')

- [docker之中进行操作](https://blog.csdn.net/u012017645/article/details/104694493?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522165771085816781667846564%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=165771085816781667846564&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-1-104694493-null-null.142^v32^pc_rank_34,185^v2^control&utm_term=mongo%20%E6%9D%83%E9%99%90%E8%AE%A4%E8%AF%81&spm=1018.2226.3001.4187)

# 一、开启认证

[参考](https://blog.csdn.net/five3/article/details/54632932?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522165771220916782391859419%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fall.%2522%257D&request_id=165771220916782391859419&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~hot_rank-10-54632932-null-null.142^v32^pc_rank_34,185^v2^control&utm_term=mongo%20%E6%9D%83%E9%99%90%E6%9C%BA%E5%88%B6&spm=1018.2226.3001.4187)

- mongod数据库服务默认是不开启用户认证的，此时任何的client端都可以连接并访问mongo服务，只有是网络可以连通即可。
  如果需要支持用户认证的功能，则必须要先主动开启该功能，主要的方式就是在启动命令的时候添加一个--auth参数即可，如下清单
  /usr/bin/mongod --config /etc/mongodb.conf --auth
  通过上述命令启动的mongo服务就会支持认证机制，client连接时如果没有带上正确的用户名和密码的话则会报错

# 二、角色分类

```
db.createUser({
	"user":"root",   -- 用户名
	"pwd":"didi1234",
	"roles": [{
		role: "root", -- 角色
		db:"admin" -- 所属数据库
	}]
})

db.createUser({
	"user":"root", 
	"pwd":"didi1234",
	"roles": [{
		role: "root", 
		db:"admin"
	}]
})
```





# 三、角色权限

[临时参考1](https://blog.csdn.net/qq_40530622/article/details/112208549?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522165771220916782391859419%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fall.%2522%257D&request_id=165771220916782391859419&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~hot_rank-7-112208549-null-null.142^v32^pc_rank_34,185^v2^control&utm_term=mongo%20%E6%9D%83%E9%99%90%E6%9C%BA%E5%88%B6&spm=1018.2226.3001.4187)