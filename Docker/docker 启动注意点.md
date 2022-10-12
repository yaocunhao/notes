# 安装

[参考链接](https://blog.csdn.net/Little_Small_Joze/article/details/113601632?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522166539319516800186510738%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=166539319516800186510738&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-2-113601632-null-null.142^v52^pc_rank_34_2,201^v3^control_1&utm_term=ubuntu18%20%E5%AE%89%E8%A3%85docker&spm=1018.2226.3001.4187)

# 值得一读

[参考链接](https://yeasy.gitbook.io/docker_practice/image/dockerfile/cmd)

# docker启动失败原因注意

- 环境变量是否配置好(注意多尝试几次，又可以忘记更新环境变量)
- 挂载的数据日志文件
  - 比如挂载的容器中有error.log文件，但是在挂载的宿主机下没有这个文件就会出错



# docker 启动uwsgi失败

- [参考链接](https://blog.csdn.net/windy135/article/details/106659155/?ops_request_misc=&request_id=&biz_id=102&utm_term=docker%20%E4%B9%8B%E4%B8%AD%E5%90%AF%E5%8A%A8uwsgi%20%E8%AF%BB%E5%8F%96%E5%AE%8C%E9%85%8D%E7%BD%AE%E6%96%87%E4%BB%B6%E5%90%8E%E5%B0%B1%E9%80%80%E4%BA%86&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-6-106659155.142^v46^pc_rank_34_default_2&spm=1018.2226.3001.4187)
- [后台进程](https://blog.csdn.net/loovelj/article/details/91610965?ops_request_misc=&request_id=&biz_id=102&utm_term=uwsgi%20%E5%A6%82%E6%9E%9C%E4%BD%BF%E7%94%A8%E4%BA%86daemonize%EF%BC%8C%E5%9C%A8docker%E4%B8%AD%E6%97%A0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-8-91610965.142^v46^pc_rank_34_default_2&spm=1018.2226.3001.4187)
  - 所以一个比较好的办法就是，在容器之中是的最后一个进程在前台运行
- 因为给日志设置了守护进程，导致不断的重启