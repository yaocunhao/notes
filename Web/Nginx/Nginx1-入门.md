# 一、基本概念

- 基本逻辑
  - Nginx一个主进程，进行网络端口的侦听 当有连接时，唤醒工作线程去处理任务
- 待探讨问题
  - 唤醒进程池怎么避免惊群问题
  - 工作线程的内存池问题(效率提升)

# 二、配置文件

## 2.1 Django和Nginx

- Nginx 和 Django的结合是通过uWSG来进行的
  - uWSGI虽然也可以作为服务器，但是Nginx作为服务器的性能要高于uWSGI
  - 因此，一般都是用Nginx作为服务器，而uWSGI充当服务器和Django之间的缓冲层
- uWSGI 服务器通过uwsgi和Django进行联系

```nginx
# mysite_nginx.conf


# 工作模式及连接数上限
events {
    worker_connections 10240;  #单个后台worker process进程的最大并发链接数
}


# the upstream component nginx needs to connect to
http {

  # 上游服务器
  upstream django {
    # server unix:///path/to/your/mysite/mysite.sock; # for a file socket
    server 127.0.0.1:3333; # for a web port socket (we'll use this first)
  }

  # configuration of the server
  # 监听的服务器模块
  server {
    # the port your site will be served on
    listen 3434;
    # the domain name it will serve for
    server_name example.com; # substitute your machine's IP address or FQDN
    charset utf-8;

    # max upload size
    # 最大上传大小
    client_max_body_size 75M; # adjust to taste

    # Django 媒体文件
    # location /media {
    #   alias /path/to/your/mysite/media; # your Django project's media files - amend as required
    # }

    # Django 静态资源
    location /static {
      alias /home/didi/map_config/map_config/static/; # your Django project's static files - amend as required
    }

    # Finally, send all non-media requests to the Django server.
    # 将其余的非媒体文件发送到Django服务器
    location / {
      uwsgi_pass django;
      include /usr/local/nginx/uwsgi_params; # the uwsgi_params file you installed
    }
  }
}
```

## 2.2 配置解析

- uwsgi_pass django
  - 此处含义是，通过uwsgi协议，连接uWSGI 接通的Django服务器
  - 如果没有写 upstream， 则可以可以更换写法为： uwsgi_pass unix:///dev/shm/map_apollo.sock (这里是uWSGI里面的socket通信套接字)
- server_name 域名
  - 通过域名访问该端口时，如果在域名列表之中则可以进行访问，如何不在该列表之中则排除
  - Mac 可在 `/etc/hosts` 之中添加临时域名解析
