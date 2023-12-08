[docker镜像使用官方文档](https://hub.docker.com/_/mysql)

[参考链接](https://blog.csdn.net/kingsleyhe/article/details/116779451?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522165097003416781818723334%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=165097003416781818723334&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-1-116779451.142^v9^control,157^v4^control&utm_term=docker+%E5%85%A5%E9%97%A8%E6%95%99%E7%A8%8B&spm=1018.2226.3001.4187)

[todo链接](https://blog.csdn.net/liang19890820/article/details/120500266?utm_medium=distribute.pc_relevant.none-task-blog-2~default~baidujs_utm_term~default-0.pc_relevant_default&spm=1001.2101.3001.4242.1&utm_relevant_index=3)

[docker ps 操作链接](https://www.jianshu.com/p/6eb192ad5b07)

# 一、启动和验证

- 启动服务
  - sudo systemctl start docker
- 验证
  - sudo docker run hello-world

# 二、docker 去除sudo权限

- [链接](https://zhuanlan.zhihu.com/p/484171630)



# 三、镜像操作

- docker 工作流程
  - docker会先在本机查找是否有要运行的镜像，如果有就运行，没有就会需要去远端docker Hub下载到本机，然后运行

- 查看docker信息
  - docker version 
  - docker info

- 查看运行的镜像

  - docker images

- 搜索镜像

  - docker search `name`

    ```python
    docker search centos
    
    NAME                              DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
    centos/systemd                    systemd enabled base container.                 108                  [OK]
    centos/mysql-57-centos7           MySQL 5.7 SQL database server                   93
    centos/postgresql-96-centos7      PostgreSQL is an advanced Object-Relational …   45
    centos/httpd-24-centos7           Platform for running Apache httpd 2.4 or bui…   44
    ```

- 下载镜像

  - docker pull `name`

    ```python
    docker search mysql5.7
    docker pull bingozhou/mysql5.7
    ```

- 删除镜像

  - 通过 docker images 查看镜像id

    ```python
    docker images
    REPOSITORY           TAG       IMAGE ID       CREATED        SIZE
    hello-world          latest    feb5d9fea6a5   7 months ago   13.3kB
    bingozhou/mysql5.7   latest    8dbbe042b8f7   4 years ago    407MB
    ```

  - docker rmi -f `id`

    ```python
    # docker rmi -f feb5d9fea6a5
    
    # didi@didi-Precision-3630-Tower:~$ docker images
    REPOSITORY           TAG       IMAGE ID       CREATED       SIZE
    bingozhou/mysql5.7   latest    8dbbe042b8f7   4 years ago   407MB
    ```

    

# 四、容器操作

[参考链接](https://blog.csdn.net/weixin_39553910/article/details/89962805?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522165286216716782246471214%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fall.%2522%257D&request_id=165286216716782246471214&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~rank_v31_ecpm-2-89962805-null-null.142^v10^control,157^v4^control&utm_term=docker%E7%9A%84run%E6%B2%A1%E5%90%AF%E5%8A%A8&spm=1018.2226.3001.4187)

## 4.1 基本概念

- 有了镜像后，就可以创建容器来运行这个镜像，通过**Docker run 镜像名字**来运行的，下面加载一个centos镜像然后运行它
- docker run `name`，就会为镜像自动创建一个容器，把这个镜像在隔离的单独的容器中运行

## 4.2 操作

- 加载镜像

  ```python
  docker search centos7 # 查找centos7 镜像
  docker pull ansible/centos7-ansible # 加载搜寻到的镜像
  ```

- 容器的运行

  - docker run  `命令参数` `镜像名称`

    - -d：后台运行容器，并且返回容器ID

    - -i：以交互模式运行容器，通常与-t 同时使用

    - -t：为容器重新分配一个伪输入终端，通常与-i同用

    - -p：指定端口映射：`主机端口：容器端口`

    - --name=`name`:为容器指定一个名称

    - -e:传递环境变量

      ```python
      # 运行容器
      # didi@didi-Precision-3630-Tower:~$ docker run -it ansible/centos7-ansible
      
      # 伪输入终端
      # [root@d49803a15610 ansible]# pwd
        /opt/ansible/ansible
      ```

- 查看运行了哪些容器 

  - docker ps -a

    ```python
    docker ps -a
    CONTAINER ID   IMAGE                     COMMAND       CREATED              STATUS                          PORTS     NAMES
    2c591efe65b0   ansible/centos7-ansible   "/bin/bash"   About a minute ago   Exited (0) About a minute ago             serene_carson
    d49803a15610   ansible/centos7-ansible   "/bin/bash"   8 minutes ago        Exited (130) 2 minutes ago                stoic_lamport
    ```

- 删除容器

  - docker rm -f `容器id`

    ```python
    # 删除
    didi@didi-Precision-3630-Tower:~$ docker rm -f 2c591efe65b0
    2c591efe65b0
    didi@didi-Precision-3630-Tower:~$ docker rm -f d49803a15610
    d49803a15610
    
    # 查看 -> 少了两个
    didi@didi-Precision-3630-Tower:~$ docker ps -a
    CONTAINER ID   IMAGE          COMMAND    CREATED          STATUS                      PORTS     NAMES
    7cddc1885690   feb5d9fea6a5   "/hello"   41 minutes ago   Exited (0) 41 minutes ago             distracted_kepler
    108e280c5409   feb5d9fea6a5   "/hello"   46 minutes ago   Exited (0) 46 minutes ago             pensive_bartik
    2c351e125a94   feb5d9fea6a5   "/hello"   2 hours ago      Exited (0) 2 hours ago                frosty_khayyam
    6345b1ad56c5   feb5d9fea6a5   "/hello"   2 hours ago      Exited (0) 2 hours ago                compassionate_villani
    
    ```

- 容器的启停

  - 启动容器：docker start `容器id`
  - 重启容器：docker restart`容器id`
  - 停止容器：docker stop `容器id`

- 进入容器

  - **容器在后台运行时**，需要进入容器操作，这时可以使用**exec**命令

  - docker exec `容器id` /bin/bash

  - docker exec -it `别名` bash

    ```python
    # 后台运行容器，取个别名ceshi
    docker run --name="ceshi"  -it -d ansible/centos7-ansible
    
    # 进入容器
    docker exec -it 1c3db9cb3b5a /bin/bash
    [root@1c3db9cb3b5a ansible]  # 显然名称换了
    
    ```

## 4.2 容器实例演示

### 4.2.1 文件拷贝

- 将主机文件复制到docker
  - docker cp `主机文件路径` `容器id:容器内路径 `

- 从docker容器内复制文件到主机

  - docker cp `容器id:容器内文件路径` `主机路径`

    ```python
    # 进入容器
    docker exec -it 1c3db9cb3b5a /bin/bash
    
    # 创建文件
    [root@1c3db9cb3b5a ansible]# echo 'hello_world' > file.txt
    
    
    # 复制到主机
    didi@didi-Precision-3630-Tower:~$ docker cp 1c3db9cb3b5a:/opt/ansible/ansible/file.txt ./
    
    didi@didi-Precision-3630-Tower:~$ cat file.txt
    hello_world
    ```

### 4.2.2 在容器中部署服务

- 部署centos 系统 [参考链接](https://www.jianshu.com/p/644e021a1e74)

- 创建一个容器，然后用这个容器部署一个镜像，再在远端连接这个镜像

  
  
  







```python
# 启动容器
docker run -it -d --name=mysql -p 8000:3306 -e MYSQL_ROOT_PASSWORD=h9426926_HAO  bingozhou/mysql5.7

# 进入容器
docker exec -it mysql bash

# 进入mysql 
root@f09f3596a6b3:/# mysql -u root -p

mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+

# 远程连接docker 之中的mysql
# mysql -u root -h 实体主机 -P 主机映射端口8000 -p 密码
# 连接出问题时在后面加上 --ssl-mode=DISABLED
```



# 五 、docker 提交镜像为容器

- 我们通过镜像运行容器，然后对容器进行了修改了，配置了我们想要的环境后，如果我们想分享给别人，让别人直接下载我配好的镜像运行，这时候就需要commit命令了，就像git 提交项目到本地仓库一样

  ```python
  # 查看运行的mysql的容器id，这个mysql是我们配置好的，这时候我们想要提交这个容器命令
  docker ps
  
  # 命令
  docker commit -m="描述信息" -a="作者" 容器id 镜像名:版本
  docker commit -m 'mysql_test' -a='ych' f09f3596a6b3 ych_mysql:1.0
  
  # 查看新建的镜像
  docker images
  REPOSITORY                TAG       IMAGE ID       CREATED          SIZE
  ych_mysql                 1.0       e42173cd3275   2 minutes ago    407MB
  
  ```

  

# 七、docker file

- [参考链接](https://blog.csdn.net/decvo6179/article/details/102078181?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522165102621416780366515915%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=165102621416780366515915&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-102078181-null-null-2~all~sobaiduend~default-1-102078181.142^v9^control,157^v4^control&utm_term=dockfile+&spm=1018.2226.3001.4187)
- 用于编写docker镜像生成过程的文件



# 八、docekr Compose

- [参考链接](https://www.qikegu.com/docs/3016)

- [Docker Compose和Docker Stack区别](https://blog.csdn.net/m0_67402564/article/details/124171654?ops_request_misc=&request_id=&biz_id=102&utm_term=docker-compose%20%20%E5%92%8C%20docker%20stack&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-0-124171654.142^v10^control,157^v4^control&spm=1018.2226.3001.4187)

## 8.1 作用

- 启动容器
- 在批量容器时，如果逐个去启动是一件很麻烦的事情，所有Compose 则是批量去启动一个容器，通过 docker-compose.ym 文件来进行配置

# 九、容器数据卷

- 数据卷挂载呢，简单来说就是为了同步容器内的数据到容器外。比如我们运行一个了一个mysql容器，如果不挂载到宿主机，那么当我们删除容器的时候，容器里面的数据也就没了。为了达到删除容器后，容器里的数据还能保存在主机上，就需要这个同步功能

- 将容器里面的目录和主机里面的目录绑定后，数据是双向的。也就是在主机中修改或者创建文件，会同步到容器中，就算容器没有运行，下次启动时也会运行。同样，容器里的目录创建和修改文件，也都会同步到主机中

- **如何挂载**

  - 在主机运行时，加-v参数就行

    ```
    docker run -itd --name 名字 -p 主机端口:容器端口 -v 主机目录:容器目录 镜像id或名字
    
    分别用-v挂载配置文件和数据目录
    docker run -itd --name mysql8 -p 主机端口:3306 -v /home/mysql/conf:/etc/mysql/conf.d -v /home/mysql/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=密码 镜像id
    
    
    docker run -idt -p 3434:4444 -v /home/didi/docker2/config/mysql.conf:/etc/mysql/conf.d/mysql.cnf  \
    -v /home/didi/docker2/data_dir/mysql:/var/lib/mysql -v /home/didi/docker2/data_dir/mysql:/var/log/mysql \ -e MYSQL_ROOT_PASSWORD=didi1234 8d8c5f97accd
    ```
  
  - ```
    数据卷挂载的时候，可以指定主机目录，也可以不指定目录，有三种方式
    
    1. -v 主机目录:容器目录
    2. -v 容器目录   这是匿名挂载
    3. -v 卷名:容器目录   这是具名挂载
    
    那么2和3会挂载到主机哪里呢？会挂载到主机 /var/lib/docker/volumes/xxxx 这里
    ```
  
    



```
docker run -itd --name sq_test -p 8888:3306 -v /home/didi/docker_test:/etc/mysql/conf.d  -v /home/didi/docker_test/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=8888 8dbbe042b8f7

```





# 十、随笔

- 当启动一个以应用程序(比如mysql)为镜像的容器时，当前的操作系统采用的是本机的系统镜像
- 端口的挂载
  - 如果没有端口的挂载，服务是不可以被外部进行访问的

- 查看日志
  - https://blog.csdn.net/sunyanchun/article/details/123640865

- 常见命令
  - [参考链接](https://blog.csdn.net/lvjianzhaoa/article/details/103859619?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522165691856816782389425875%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=165691856816782389425875&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-1-103859619-null-null.142^v30^pc_rank_34,185^v2^control&utm_term=docker-compose+volumes%E8%AF%A6%E8%A7%A3&spm=1018.2226.3001.4187)

- ssh链接不上mysql时
  - --ssl-mode=DISABLED

```
$ sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) \
  stable"
```



# 十一、Docker 安装

- [link](https://docs.docker.com/engine/install/ubuntu/#install-using-the-repository)

# 十二、Docker-compose 安装

- [docker-compose安装](https://github.com/docker/compose/releases)
  - 直接下载二进制文件
