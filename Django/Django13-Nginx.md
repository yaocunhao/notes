# 一、什么是Nginx以及其主要作用

- Nginx (engine x) 是一个高性能的 HTTP 和反向代理 Web 服务器，同时也提供了 IMAP/POP3/SMTP 服务
- Nginx 作为一款轻量级的Web 服务器、反向代理服务器及电子邮件代理服务器，它以自身的稳定性、并发能力强和超低的系统资源消耗而闻名
- Nginx 的主要作用是作为 Web 服务器的反向代理服务器，**实现 Web 服务器的负载均衡以及站点资源的动静分离**

# 二、概念悉知

## 2.1 反向代理服务器

- 反向代理服务器**位于客户端用户与目标服务器之间**，但是**对于用户而言，反向代理服务器就相当于目标服务器，即用户直接访问反向代理服务器就可以获得目标服务器的资源**
- 同时，用户不需要知道目标服务器的地址，也无须在用户端作任何设定。反向代理服务器通常可用来作为 Web 加速，用来提升用户访问站点的速度，所以使用 Nignx 作为 Web 服务器的前置服务器可以**降低网络和服务器的负载，从而提高访问效率**

## 2.2 负载均衡

- 它的意思就是**将工作任务分摊到多个操作单元上进行执行**，比如 Web 服务器、FTP 服务器、企业关键应用服务器和其它关键任务服务器等，从而共同完成工作任务
- 单个服务器解决不了，我们增加服务器的数量，然后**将请求分发到各个服务器上面**，将原先请求到单个服务器上面的情况改为**将请求分发到多个服务器上，将负载分发到不同的服务器**，这就是负载均衡

## 2.3 动静分离

- 所谓动静分离就是为了加快网站的解析速度，可以**把动态页面和静态页面由不同的服务器来解析**，加快解析速度，**降低单个服务器的压力**。进而提升整个服务访问性能和可维护性

## 2.4 Nginx应用场景

- 作为虚拟主机，它可以实现在一台服务器虚拟出多个网站
- 用来充当 HTTP 服务器。Nginx 是一个 HTTP 服务器可以独立提供 HTTP 服务，也可以做网页静态服务器
- 实现反向代理、负载均衡。当网站的访问量达到一定程度后，单台服务器不能满足用户的请求，这时就需要用多台服务器组成服务集群并把 Nginx 做为反向代理服务器，实现多台服务器可以平均分担负载的目的，这样就不会造成某台服务器负载过高而宕机，而集群中另一台服务器闲置的情况

# 三、实现Nginx+uWSGI+Django项目部署

## 3.1 项目部署分析

- 在项目部署中，Nginx在项目中起着关键作用，Nginx接收客户端的请求（一个Nginx服务器能够同一时刻支撑 5 万的并发量），并且将请求分为动态请求和静态请求
- uWSGI则将Nginx接收到的请求转变为Django Web框架可以识别的形式，并且发送给Django
- Django处理请求之后，将响应放回给uWSGI，最终由Nginx返回给客户端响应![image-20220525164048599](../../../Library/Application Support/typora-user-images/image-20220525164048599.png)

## 3.2 nginx 安装

- [官网]([http://nginx.org/en/download.html](https://nginx.org/en/download.html))
- 配置文件（mac下）
  - brew nginx
  - 配置文件位置 /usr/local/etc/nginx
- 启动/退出
  - `./nginx -c /usr/local/etc/nginx/nginx.conf.default  `(mac下)
  - 退出 nginx -quit

- 修改uUWSGI通信方式

  ```python
  [uwsgi]
  # 删除掉http方式
  # http=127.0.0.1:8000
  # 改为socket
  socket=127.0.0.1:8000 
  ```

## 3.3 Nginx配置静态文件路径

- 创建文件夹 `static_test/static`

- 在setting中设置 `STATIC_ROOT=static_test/static`

- 收集静态文件 `python3 manage.py collectstatic`

- 修改Nginx配置,在server节点下添加新的location项，指向uWSGI的ip与端口(即8000端口)(**动态路由**)

  - 即服务都是访问的nginx的端口，nginx再将请求分发出去

  ```python
  #在server节点下添加新的location项，指向uWSGI的ip与端口(即8000端口)。
  server {
      ...
      location / {
          uwsgi_pass 127.0.0.1:8000; #将请求转发重定向到127.0.0.1的8000端口
          include /etc/nginx/uwsgi_params;#将所有的参数转到uwsgi下
      }
      ...
  }
  ```

  

- 修改Nginx配置,server 中新添加 location /static 静态文件路由配置(**静态路由**)

  ```python
  server {
      ...
      location /static {
          #root static文件夹所在绝对路径,示例如下:
          static_test/static; # 重定向,自动找到static目录
      }
      ...
  }
  ```

## 3.4 注意点

- 进行访问的时候Nginx侦听的端口是8080 所以访问的端口也需要改成8080

- 整体流程

  - 创建静态文件夹

  - 在setting中设置 `STATIC_ROOT=static_test/static`

  - 修改uwsgi 通信方式

  - 收集静态文件                   

    ![image-20220525174804263](../../../Library/Application Support/typora-user-images/image-20220525174804263.png)

  - 修改在nginx配置下添加静态资源路径
  - 在setting下添加STATIC_ROOT
  - 启动nginx和uwsgi
  - 通过8080(nginx侦听端口访问)

