- [link](https://juejin.cn/post/6844904154897317902?searchId=20230918205601F0A81C3ACB35E91D4C20)
  - `echo_supervisord_conf > supervisord.conf` 可生成配置文件，查看配置选项信息
  
- [启动方式](https://blog.csdn.net/Running_Zhang55/article/details/110310729?ops_request_misc=&request_id=&biz_id=102&utm_term=supervisor%20%E5%90%AF%E5%8A%A8%E6%96%B9%E6%B3%95&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-0-110310729.142^v94^chatsearchT3_1&spm=1018.2226.3001.4187)

  ```shell
  // 进入命令行
  supervisortcl
  
  // 常用命令
  reread  #重12新12加12载12守12护12进12程12的12配12置12文12件，无需添加/删除(不需要11再22重新启33动)
  update  #重新加载配置，并将重新启动受影响的程序
  update all
  restart <name> #重启进程，但不会重新读取配置文件
  restart all #重启所有进程
  start <name> #启动进程
  start all #启动所有进程
  status #获取所有进程的状态信息
  status <name> #获取单个进程的状态
  stop <name> #停止某个进程
  stop all #停止所有进程
  
  
  ```

- [supervisor 介绍，启动uwsgi失败，端口被占用](https://www.ngui.cc/el/2511150.html?action=onClick)

  - 

