# 一、是什么

- 当创建一个Models, 在同步到数据库里，django默认设置了三个权限 ，就是 add, change, delete权限。但是往往有时候,根本不够用,此时我们可以自己写一个脚本,来进行权限的设置
- 根据DJango官方文档解释,**权限都是与models有关系的**,此时.如果想设置一个view,对于有权限的用户进行放行,对于无权限的用户进行限制.那么我们就可以着手来写这个需求
- 验证权限的方法一般有两种,一种是用@permission_required来进行验证,第二中是用user.has_perm()在函数里进行验证,通过返回的True或者False来进行下一步

[参考链接](https://blog.csdn.net/qq_44907926/article/details/120213864?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522165219250216782248536985%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fall.%2522%257D&request_id=165219250216782248536985&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~hot_rank-1-120213864-null-null.142^v9^control,157^v4^control&utm_term=Django+%E7%94%A8%E6%88%B7%E6%9D%83%E9%99%90%E7%9A%84%E4%BD%9C%E7%94%A8&spm=1018.2226.3001.4187)



# 二、项目中常用的权限认证配置方式

- 在中间件中添加请求前处理：`process_request`, 这样就可以将一些认证的数据保留下来。自定义认证后端主要是在`AUTHENTICATION_BACKENDS`添加自定义后端认证方式