[参考链接](https://www.runoob.com/w3cnote/nginx-install-and-config.html)

# 一、什么是Nginx以及其主要作用

- Nginx (engine x) 是一个高性能的 HTTP 和反向代理 Web 服务器，同时也提供了 IMAP/POP3/SMTP 服务
- Nginx 作为一款轻量级的Web 服务器、反向代理服务器及电子邮件代理服务器，它以自身的稳定性、并发能力强和超低的系统资源消耗而闻名
- Nginx 的主要作用是作为 Web 服务器的反向代理服务器，**实现 Web 服务器的负载均衡以及站点资源的动静分离**

# 二、概念悉知

## 2.1 反向代理服务器

- 反向代理服务器**位于客户端用户与目标服务器之间**，但是**对于用户而言，反向代理服务器就相当于目标服务器，即用户直接访问反向代理服务器就可以获得目标服务器的资源**
- 同时，用户不需要知道目标服务器的地址，也无须在用户端作任何设定。反向代理服务器通常可用来作为 Web 加速，用来提升用户访问站点的速度，所以使用 Nignx 作为 Web 服务器的前置服务器可以**降低网络和服务器的负载，从而提高访问效率**

## 2.2 正向代理

- 正向代理，代理的是客户端，对应服务器而言，不知道客户端的地址
- 客户端请求的是代理服务器，代理服务器再将请求转给服务器

## 2.3 负载均衡

- 它的意思就是**将工作任务分摊到多个操作单元上进行执行**，比如 Web 服务器、FTP 服务器、企业关键应用服务器和其它关键任务服务器等，从而共同完成工作任务
- 单个服务器解决不了，我们增加服务器的数量，然后**将请求分发到各个服务器上面**，将原先请求到单个服务器上面的情况改为**将请求分发到多个服务器上，将负载分发到不同的服务器**，这就是负载均衡

## 2.4 动静分离

- 所谓动静分离就是为了加快网站的解析速度，可以**把动态页面和静态页面由不同的服务器来解析**，加快解析速度，**降低单个服务器的压力**。进而提升整个服务访问性能和可维护性

## 2.5 Nginx应用场景

- 作为虚拟主机，它可以实现在一台服务器虚拟出多个网站

- 用来充当 HTTP 服务器。Nginx 是一个 HTTP 服务器可以独立提供 HTTP 服务，也可以做网页静态服务器

- 实现反向代理、负载均衡。当网站的访问量达到一定程度后，单台服务器不能满足用户的请求，这时就需要用多台服务器组成服务集群并把 Nginx 做为反向代理服务器，实现多台服务器可以平均分担负载的目的，这样就不会造成某台服务器负载过高而宕机，而集群中另一台服务器闲置的情况

  

# 三、Nginx 的使用方法

## 3.1 一些操作命令

- 启动 nginx

  - nginx -c 绝对路径/nginx.conf 启动nginx服务

- 在nginx启动的状态下

  - 停止 nginx -s stop

  - 重新加载配置  nginx -s reload

    ```shell
      -v            : show version and exit
      -V            : show version and configure options then exit
      -t            : test configuration and exit # 检查配置是否正确
      -T            : test configuration, dump it and exit
      -q            : suppress non-error messages during configuration testing
      -s signal     : send signal to a master process: stop, quit, reopen, reload # 停止命令
      -p prefix     : set prefix path (default: /usr/local/Cellar/nginx/1.21.6_1/) # 设置安装路径
      -e filename   : set error log file (default: /usr/local/var/log/nginx/error.log) # 设置日志文件
      -c filename   : set configuration file (default: /usr/local/etc/nginx/nginx.conf) # 设置配置文件名称
      -g directives : set global directives out of configuration file #  全局指令脱离配置文件
    ```

# 四、配置文件

## 4.1 配置文件结构

- nginx 由模块组成，这些模块由配置文件中指定的指令控制
- 指令分为简单指令和块指令
  - 一个简单的指令由名称和参数组成，由空格分隔并以分号 ( `;`) 结尾
  - 块指令与简单指令具有相同的结构，但它不是以分号结尾，而是以一组由大括号 (`{`和`}`) 包围的附加指令
    - 如果块指令可以在大括号内包含其他指令，则称为上下文（例如： [events](http://nginx.org/en/docs/ngx_core_module.html#events)、 [http](http://nginx.org/en/docs/http/ngx_http_core_module.html#http)、 [server](http://nginx.org/en/docs/http/ngx_http_core_module.html#server)和 [location](http://nginx.org/en/docs/http/ngx_http_core_module.html#location)）
    - 放置在配置文件中，任何上下文之外的指令都被认为在主上下文中，比如`events`和`htpp`在主上下文中，`server`在`http`中，`location`在`server`之中
- 进一步阐述指令
  - server : 用于定义服务，http中可以有多个server块
  - listen : 指定服务器侦听请求的IP地址和端口，如果省略地址，服务器将侦听所有地址，如果省略端口，则使用标准端口
  - server_name : 服务名称，用于配置域名(需要注册域名，当有流量进来时，会优先匹配IP然后才匹配端口)
  - location : 用于配置映射路径uri对应的配置，一个server中可以有多个location, location后面跟一个uri,可以是一个正则表达式, / 表示匹配任意路径, 当客户端访问的路径满足这个uri时就会执行location块里面的代码
  - root : **根路径，用户访问的资源 = root + url**
    - 当访问`http://localhost/test.html`，“/test.html”会匹配到”/”uri, 找到root为`/usr/local/var/www/html`，用户访问的资源物理地址=`root + uri = /usr/local/var/www/html + /test.html=/usr/local/var/www/html/test.html`
    - root 表示资源的根目录，当root在main上下文中时，表示所有资源的根路径，当在localtion上下文中时表示当前路由的上下文路径
  - index : **设置首页，**
    - 当只访问`server_name`时后面不跟任何路径时，访问的资源就是 `root/index`

## 4.4 静态服务内容

- 通过在`http`上下文中配置一个`server`，设置一个静态资源路由

  ```nginx
  events {
      worker_connections 1024;
  }
  
  http {
  	server {
  			location / { # 指定了匹配的前缀 
  				root /data/www; # 根目录下的某个路径
  			}
  			
      	# 比如此时访问网址为 ip:80/images/index
        # 就会被路由到 /data/images/index
  			location /images/ { 
  					root /data;
  			}
  	}
  }
  ```
  
  

## 4.5 代理服务器设置

```shell
events {
    worker_connections 1024;
}

http {
	server {

    # 默认端口80

			location / {
        # 代理服务器，将流量转移至配置的路由主机中
        proxy_pass http://172.25.16.53:8080; # 指定服务器名称和端口,转到本地的8080端口
    }

		location ~ \.(gif|jpg|png)$ { # 匹配所有以.gif、.jpg或.png结尾的uri
    	root /data/images;
		}
	}

	server {
    listen 8080; # 侦听的端口 8000
    root /data/www; # 映射的目录,现在的root指令被放在了server的上下文之中，表示当前server块的所有资源根路径都是该模块·
    location / {}
	}
}
```



